---
title: 新しいデータベース - EF6 code First
author: divega
ms.date: 10/23/2016
ms.assetid: 2df6cb0a-7d8b-4e28-9d05-e2b9a90125af
ms.openlocfilehash: a19db575b685cde98509fff4a0efaf26106b26bc
ms.sourcegitcommit: 269c8a1a457a9ad27b4026c22c4b1a76991fb360
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46284123"
---
# <a name="code-first-to-a-new-database"></a><span data-ttu-id="d38e2-102">新しいデータベースの code First</span><span class="sxs-lookup"><span data-stu-id="d38e2-102">Code First to a New Database</span></span>
<span data-ttu-id="d38e2-103">このビデオ、およびステップ バイ ステップ チュートリアルでは、新しいデータベースを対象とする Code First の開発の概要を提供します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-103">This video and step-by-step walkthrough provide an introduction to Code First development targeting a new database.</span></span> <span data-ttu-id="d38e2-104">このシナリオには、存在しないデータベースを対象とするが含まれていますと Code First が作成、または空のデータベースを Code First が新しいテーブルを追加します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-104">This scenario includes targeting a database that doesn’t exist and Code First will create, or an empty database that Code First will add new tables to.</span></span> <span data-ttu-id="d38e2-105">コードを最初に使用すると、C を使用して、モデル定義\#または VB.Net クラス。</span><span class="sxs-lookup"><span data-stu-id="d38e2-105">Code First allows you to define your model using C\# or VB.Net classes.</span></span> <span data-ttu-id="d38e2-106">必要に応じてクラスやプロパティ、または fluent API を使用して属性を使用して追加の構成を実行できます。</span><span class="sxs-lookup"><span data-stu-id="d38e2-106">Additional configuration can optionally be performed using attributes on your classes and properties or by using a fluent API.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="d38e2-107">ビデオを見る</span><span class="sxs-lookup"><span data-stu-id="d38e2-107">Watch the video</span></span>
<span data-ttu-id="d38e2-108">このビデオでは、新しいデータベースを対象とする Code First の開発の概要を提供します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-108">This video provides an introduction to Code First development targeting a new database.</span></span> <span data-ttu-id="d38e2-109">このシナリオには、存在しないデータベースを対象とするが含まれていますと Code First が作成、または空のデータベースを Code First が新しいテーブルを追加します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-109">This scenario includes targeting a database that doesn’t exist and Code First will create, or an empty database that Code First will add new tables to.</span></span> <span data-ttu-id="d38e2-110">まず、コードを使用すると、C# または VB.Net のクラスを使用してモデルを定義できます。</span><span class="sxs-lookup"><span data-stu-id="d38e2-110">Code First allows you to define your model using C# or VB.Net classes.</span></span> <span data-ttu-id="d38e2-111">必要に応じてクラスやプロパティ、または fluent API を使用して属性を使用して追加の構成を実行できます。</span><span class="sxs-lookup"><span data-stu-id="d38e2-111">Additional configuration can optionally be performed using attributes on your classes and properties or by using a fluent API.</span></span>

<span data-ttu-id="d38e2-112">**提供**: [Rowan Miller](http://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="d38e2-112">**Presented By**: [Rowan Miller](http://romiller.com/)</span></span>

<span data-ttu-id="d38e2-113">**ビデオ**: [WMV](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv) | [MP4](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)</span><span class="sxs-lookup"><span data-stu-id="d38e2-113">**Video**: [WMV](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv) | [MP4](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="d38e2-114">前提条件</span><span class="sxs-lookup"><span data-stu-id="d38e2-114">Pre-Requisites</span></span>

<span data-ttu-id="d38e2-115">少なくとも Visual Studio 2010 が必要がありますか、このチュートリアルを実行する Visual Studio 2012 がインストールされています。</span><span class="sxs-lookup"><span data-stu-id="d38e2-115">You will need to have at least Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="d38e2-116">Visual Studio 2010 を使用している場合も必要になりますが[NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="d38e2-116">If you are using Visual Studio 2010, you will also need to have [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

## <a name="1-create-the-application"></a><span data-ttu-id="d38e2-117">1.アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="d38e2-117">1. Create the Application</span></span>

<span data-ttu-id="d38e2-118">物事を複雑に Code First を使用して、データ アクセスを実行する基本的なコンソール アプリケーションの構築になります。</span><span class="sxs-lookup"><span data-stu-id="d38e2-118">To keep things simple we’re going to build a basic console application that uses Code First to perform data access.</span></span>

-   <span data-ttu-id="d38e2-119">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="d38e2-119">Open Visual Studio</span></span>
-   <span data-ttu-id="d38e2-120">**ファイル -&gt;新機能 -&gt;プロジェクト.**</span><span class="sxs-lookup"><span data-stu-id="d38e2-120">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="d38e2-121">選択**Windows**左側のメニューと**コンソール アプリケーション**</span><span class="sxs-lookup"><span data-stu-id="d38e2-121">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="d38e2-122">入力**CodeFirstNewDatabaseSample**名として</span><span class="sxs-lookup"><span data-stu-id="d38e2-122">Enter **CodeFirstNewDatabaseSample** as the name</span></span>
-   <span data-ttu-id="d38e2-123">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-123">Select **OK**</span></span>

## <a name="2-create-the-model"></a><span data-ttu-id="d38e2-124">2.モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-124">2. Create the Model</span></span>

<span data-ttu-id="d38e2-125">クラスを使用して、非常に単純なモデルを定義してみましょう。</span><span class="sxs-lookup"><span data-stu-id="d38e2-125">Let’s define a very simple model using classes.</span></span> <span data-ttu-id="d38e2-126">定義していますだけに、Program.cs ファイルが個別のファイルや可能性のある別のプロジェクトをクラスに分割するが、実際のアプリケーションにします。</span><span class="sxs-lookup"><span data-stu-id="d38e2-126">We’re just defining them in the Program.cs file but in a real world application you would split your classes out into separate files and potentially a separate project.</span></span>

<span data-ttu-id="d38e2-127">Program.cs で、プログラムのクラス定義の下には、次の 2 つのクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-127">Below the Program class definition in Program.cs add the following two classes.</span></span>

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }

    public virtual List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public int BlogId { get; set; }
    public virtual Blog Blog { get; set; }
}
```

<span data-ttu-id="d38e2-128">行って 2 つのナビゲーション プロパティ (Blog.Posts および Post.Blog) 仮想がわかります。</span><span class="sxs-lookup"><span data-stu-id="d38e2-128">You’ll notice that we’re making the two navigation properties (Blog.Posts and Post.Blog) virtual.</span></span> <span data-ttu-id="d38e2-129">これにより、Entity Framework の遅延読み込み機能です。</span><span class="sxs-lookup"><span data-stu-id="d38e2-129">This enables the Lazy Loading feature of Entity Framework.</span></span> <span data-ttu-id="d38e2-130">遅延読み込みでは、それらにアクセスするときに、これらのプロパティの内容がデータベースから自動的に読み込まれることを意味します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-130">Lazy Loading means that the contents of these properties will be automatically loaded from the database when you try to access them.</span></span>

## <a name="3-create-a-context"></a><span data-ttu-id="d38e2-131">3.コンテキストを作成します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-131">3. Create a Context</span></span>

<span data-ttu-id="d38e2-132">ここには、クエリとデータを保存することができます、データベースとのセッションを表す、派生コンテキストの定義します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-132">Now it’s time to define a derived context, which represents a session with the database, allowing us to query and save data.</span></span> <span data-ttu-id="d38e2-133">System.Data.Entity.DbContext から派生し、型指定された DbSet を公開するコンテキストを定義します&lt;TEntity&gt;私たちのモデルの各クラスに対して。</span><span class="sxs-lookup"><span data-stu-id="d38e2-133">We define a context that derives from System.Data.Entity.DbContext and exposes a typed DbSet&lt;TEntity&gt; for each class in our model.</span></span>

<span data-ttu-id="d38e2-134">今すぐ EntityFramework NuGet パッケージを追加する必要があります、Entity Framework の型を使用を開始しています。</span><span class="sxs-lookup"><span data-stu-id="d38e2-134">We’re now starting to use types from the Entity Framework so we need to add the EntityFramework NuGet package.</span></span>

-   <span data-ttu-id="d38e2-135">**プロジェクト:&gt; NuGet パッケージを管理しています.**</span><span class="sxs-lookup"><span data-stu-id="d38e2-135">**Project –&gt; Manage NuGet Packages…**</span></span>
    <span data-ttu-id="d38e2-136">注: していない場合、 **NuGet パッケージの管理.**</span><span class="sxs-lookup"><span data-stu-id="d38e2-136">Note: If you don’t have the **Manage NuGet Packages…**</span></span> <span data-ttu-id="d38e2-137">オプションをインストールする必要があります、 [NuGet の最新バージョン](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)</span><span class="sxs-lookup"><span data-stu-id="d38e2-137">option you should install the [latest version of NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)</span></span>
-   <span data-ttu-id="d38e2-138">選択、**オンライン** タブ</span><span class="sxs-lookup"><span data-stu-id="d38e2-138">Select the **Online** tab</span></span>
-   <span data-ttu-id="d38e2-139">選択、 **EntityFramework**パッケージ</span><span class="sxs-lookup"><span data-stu-id="d38e2-139">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="d38e2-140">クリックして**インストール**</span><span class="sxs-lookup"><span data-stu-id="d38e2-140">Click **Install**</span></span>

<span data-ttu-id="d38e2-141">使用して、追加の Program.cs の上部にある System.Data.Entity ステートメント。</span><span class="sxs-lookup"><span data-stu-id="d38e2-141">Add a using statement for System.Data.Entity at the top of Program.cs.</span></span>

``` csharp
using System.Data.Entity;
```

<span data-ttu-id="d38e2-142">投稿の下は、Program.cs 内のクラスは、次の派生コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-142">Below the Post class in Program.cs add the following derived context.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```

<span data-ttu-id="d38e2-143">Program.cs を今すぐに含められるものの完全な一覧を次に示します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-143">Here is a complete listing of what Program.cs should now contain.</span></span>

``` csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data.Entity;

namespace CodeFirstNewDatabaseSample
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Name { get; set; }

        public virtual List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }

    public class BloggingContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
    }
}
```

<span data-ttu-id="d38e2-144">格納して、データの取得を開始する必要があります。 すべてのコードです。</span><span class="sxs-lookup"><span data-stu-id="d38e2-144">That is all the code we need to start storing and retrieving data.</span></span> <span data-ttu-id="d38e2-145">当然ながら、バック グラウンドで起こっている非常にビットがあるし、見ていきますにあることですが、まずこの後すぐにみましょう動作を確認します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-145">Obviously there is quite a bit going on behind the scenes and we’ll take a look at that in a moment but first let’s see it in action.</span></span>

## <a name="4-reading--writing-data"></a><span data-ttu-id="d38e2-146">4.読み取りとデータの書き込み</span><span class="sxs-lookup"><span data-stu-id="d38e2-146">4. Reading & Writing Data</span></span>

<span data-ttu-id="d38e2-147">次に示すように、Program.cs の Main メソッドを実装します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-147">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="d38e2-148">このコードは、コンテキストの新しいインスタンスを作成、し、それを使用して、新しいブログを挿入します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-148">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="d38e2-149">タイトルでアルファベット順では、データベースからすべてのブログを取得するのに LINQ クエリを使用します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-149">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

``` csharp
class Program
{
    static void Main(string[] args)
    {
        using (var db = new BloggingContext())
        {
            // Create and save a new Blog
            Console.Write("Enter a name for a new Blog: ");
            var name = Console.ReadLine();

            var blog = new Blog { Name = name };
            db.Blogs.Add(blog);
            db.SaveChanges();

            // Display all Blogs from the database
            var query = from b in db.Blogs
                        orderby b.Name
                        select b;

            Console.WriteLine("All blogs in the database:");
            foreach (var item in query)
            {
                Console.WriteLine(item.Name);
            }

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

<span data-ttu-id="d38e2-150">アプリケーションを実行し、テストを実行できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d38e2-150">You can now run the application and test it out.</span></span>

```
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
### <a name="wheres-my-data"></a><span data-ttu-id="d38e2-151">データがあるでしょうか。</span><span class="sxs-lookup"><span data-stu-id="d38e2-151">Where’s My Data?</span></span>

<span data-ttu-id="d38e2-152">規則により、DbContext がデータベースを作成しました。</span><span class="sxs-lookup"><span data-stu-id="d38e2-152">By convention DbContext has created a database for you.</span></span>

-   <span data-ttu-id="d38e2-153">ローカル SQL Express のインスタンスが使用可能な (Visual Studio 2010 では既定でインストールされている) 場合、Code First が作成、データベース インスタンス上</span><span class="sxs-lookup"><span data-stu-id="d38e2-153">If a local SQL Express instance is available (installed by default with Visual Studio 2010) then Code First has created the database on that instance</span></span>
-   <span data-ttu-id="d38e2-154">SQL Express を使用できないかどうか、Code First を使用して[LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) (Visual Studio 2012 では、既定でインストール)</span><span class="sxs-lookup"><span data-stu-id="d38e2-154">If SQL Express isn’t available then Code First will try and use [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) (installed by default with Visual Studio 2012)</span></span>
-   <span data-ttu-id="d38e2-155">今回の場合に、派生コンテキストの完全修飾名の後に、データベースの名前は**CodeFirstNewDatabaseSample.BloggingContext**</span><span class="sxs-lookup"><span data-stu-id="d38e2-155">The database is named after the fully qualified name of the derived context, in our case that is **CodeFirstNewDatabaseSample.BloggingContext**</span></span>

<span data-ttu-id="d38e2-156">これらは、既定の規則と Code First を使用するデータベースを変更するさまざまな方法が、詳細については、**モデルとデータベース接続の DbContext の検出**トピック。</span><span class="sxs-lookup"><span data-stu-id="d38e2-156">These are just the default conventions and there are various ways to change the database that Code First uses, more information is available in the **How DbContext Discovers the Model and Database Connection** topic.</span></span>
<span data-ttu-id="d38e2-157">Visual Studio でサーバー エクスプ ローラーを使用してこのデータベースに接続することができます。</span><span class="sxs-lookup"><span data-stu-id="d38e2-157">You can connect to this database using Server Explorer in Visual Studio</span></span>

-   <span data-ttu-id="d38e2-158">**ビュー -&gt;サーバー エクスプ ローラー**</span><span class="sxs-lookup"><span data-stu-id="d38e2-158">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="d38e2-159">右クリックして**データ接続**選択**接続の追加.**</span><span class="sxs-lookup"><span data-stu-id="d38e2-159">Right click on **Data Connections** and select **Add Connection…**</span></span>
-   <span data-ttu-id="d38e2-160">場合、まだデータベースに接続して、サーバー エクスプ ローラーから前に、データ ソースとして Microsoft SQL Server を選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d38e2-160">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![データ ソースの選択](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="d38e2-162">LocalDB または SQL Express をインストールしたものによってのいずれかに接続します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-162">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>

<span data-ttu-id="d38e2-163">Code First を作成したスキーマを検査できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d38e2-163">We can now inspect the schema that Code First created.</span></span>

![初期のスキーマ](~/ef6/media/schemainitial.png)

<span data-ttu-id="d38e2-165">DbContext を出して DbSet プロパティ定義を調べることで、モデルに含めるには、どのようなクラスです。</span><span class="sxs-lookup"><span data-stu-id="d38e2-165">DbContext worked out what classes to include in the model by looking at the DbSet properties that we defined.</span></span> <span data-ttu-id="d38e2-166">Code First の規則の既定のセットを使用して、テーブルと列名を決定する、データ型を決定する、主キーなどを検索します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-166">It then uses the default set of Code First conventions to determine table and column names, determine data types, find primary keys, etc.</span></span> <span data-ttu-id="d38e2-167">このチュートリアルの後半では、これらの規則を無効にする方法を紹介します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-167">Later in this walkthrough we’ll look at how you can override these conventions.</span></span>

## <a name="5-dealing-with-model-changes"></a><span data-ttu-id="d38e2-168">5.モデルの変更を処理します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-168">5. Dealing with Model Changes</span></span>

<span data-ttu-id="d38e2-169">ここには、データベース スキーマを更新する必要もありますこれらの変更を行ったときに、モデルでは、変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="d38e2-169">Now it’s time to make some changes to our model, when we make these changes we also need to update the database schema.</span></span> <span data-ttu-id="d38e2-170">これを行うには、Code First Migrations、または移行を略してと呼ばれる機能を使用するでしょう。</span><span class="sxs-lookup"><span data-stu-id="d38e2-170">To do this we are going to use a feature called Code First Migrations, or Migrations for short.</span></span>

<span data-ttu-id="d38e2-171">移行により、データベース スキーマをアップグレード (およびダウン グレード) する方法について説明する手順の順序付けされたセットを作成します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-171">Migrations allows us to have an ordered set of steps that describe how to upgrade (and downgrade) our database schema.</span></span> <span data-ttu-id="d38e2-172">次の手順は、移行と呼ばれるには、適用する変更について説明したいくつかのコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="d38e2-172">Each of these steps, known as a migration, contains some code that describes the changes to be applied.</span></span> 

<span data-ttu-id="d38e2-173">最初の手順では、BloggingContext を Code First Migrations を有効にします。</span><span class="sxs-lookup"><span data-stu-id="d38e2-173">The first step is to enable Code First Migrations for our BloggingContext.</span></span>

-   <span data-ttu-id="d38e2-174">**ツール -&gt;ライブラリ パッケージ マネージャー -&gt;パッケージ マネージャー コンソール**</span><span class="sxs-lookup"><span data-stu-id="d38e2-174">**Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
-   <span data-ttu-id="d38e2-175">パッケージ マネージャー コンソールで **Enable-Migrations** コマンドを実行します</span><span class="sxs-lookup"><span data-stu-id="d38e2-175">Run the **Enable-Migrations** command in Package Manager Console</span></span>
-   <span data-ttu-id="d38e2-176">新しい Migrations フォルダーが 2 つの項目を含む、プロジェクトに追加されています。</span><span class="sxs-lookup"><span data-stu-id="d38e2-176">A new Migrations folder has been added to our project that contains two items:</span></span>
    -   <span data-ttu-id="d38e2-177">**Configuration.cs** – このファイルには、移行するための移行を使用する設定が含まれています。 BloggingContext します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-177">**Configuration.cs** – This file contains the settings that Migrations will use for migrating BloggingContext.</span></span> <span data-ttu-id="d38e2-178">このチュートリアルでは何も変更する必要はありませんが、ここでは、シード データを他のデータベースで登録プロバイダー名前空間の変更を指定することができますなどで、移行が生成されることです。</span><span class="sxs-lookup"><span data-stu-id="d38e2-178">We don’t need to change anything for this walkthrough, but here is where you can specify seed data, register providers for other databases, changes the namespace that migrations are generated in etc.</span></span>
    -   <span data-ttu-id="d38e2-179">**&lt;タイムスタンプ&gt;\_InitialCreate.cs** – これは、最初の移行、ブログや投稿のテーブルを含む 1 つに空のデータベースからをデータベースに既に適用されている変更を表します.</span><span class="sxs-lookup"><span data-stu-id="d38e2-179">**&lt;timestamp&gt;\_InitialCreate.cs** – This is your first migration, it represents the changes that have already been applied to the database to take it from being an empty database to one that includes the Blogs and Posts tables.</span></span> <span data-ttu-id="d38e2-180">Code First からへの移行、移行に変換されたことにしましたところ、これらのテーブルを自動的に作成できるようにします。</span><span class="sxs-lookup"><span data-stu-id="d38e2-180">Although we let Code First automatically create these tables for us, now that we have opted in to Migrations they have been converted into a Migration.</span></span> <span data-ttu-id="d38e2-181">コードは、まず、この移行が既に適用されているローカル データベースには記録もします。</span><span class="sxs-lookup"><span data-stu-id="d38e2-181">Code First has also recorded in our local database that this Migration has already been applied.</span></span> <span data-ttu-id="d38e2-182">ファイル名のタイムスタンプは、並べ替えの目的で使用されます。</span><span class="sxs-lookup"><span data-stu-id="d38e2-182">The timestamp on the filename is used for ordering purposes.</span></span>

    <span data-ttu-id="d38e2-183">これで、モデルに変更を加える、ブログ クラスに Url プロパティを追加しましょう。</span><span class="sxs-lookup"><span data-stu-id="d38e2-183">Now let’s make a change to our model, add a Url property to the Blog class:</span></span>

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }

    public virtual List<Post> Posts { get; set; }
}
```

-   <span data-ttu-id="d38e2-184">実行、 **Add-migration AddUrl**パッケージ マネージャー コンソール コマンド。</span><span class="sxs-lookup"><span data-stu-id="d38e2-184">Run the **Add-Migration AddUrl** command in Package Manager Console.</span></span>
    <span data-ttu-id="d38e2-185">Add-migration コマンドは、最後の移行後の変更を確認し、検出したすべての変更と新しい移行をスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="d38e2-185">The Add-Migration command checks for changes since your last migration and scaffolds a new migration with any changes that are found.</span></span> <span data-ttu-id="d38e2-186">移行は名を紹介します。この場合、移行 'AddUrl' と呼んでいます。</span><span class="sxs-lookup"><span data-stu-id="d38e2-186">We can give migrations a name; in this case we are calling the migration ‘AddUrl’.</span></span>
    <span data-ttu-id="d38e2-187">スキャフォールディングされたコードは、dbo に文字列データを保持できる、Url の列を追加する必要があることを示しています。ブログのテーブルです。</span><span class="sxs-lookup"><span data-stu-id="d38e2-187">The scaffolded code is saying that we need to add a Url column, that can hold string data, to the dbo.Blogs table.</span></span> <span data-ttu-id="d38e2-188">必要な場合は、スキャフォールディングされたコードを編集しましたでしたがここでする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d38e2-188">If needed, we could edit the scaffolded code but that’s not required in this case.</span></span>

``` csharp
namespace CodeFirstNewDatabaseSample.Migrations
{
    using System;
    using System.Data.Entity.Migrations;

    public partial class AddUrl : DbMigration
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

-   <span data-ttu-id="d38e2-189">実行、 **Update-database**パッケージ マネージャー コンソール コマンド。</span><span class="sxs-lookup"><span data-stu-id="d38e2-189">Run the **Update-Database** command in Package Manager Console.</span></span> <span data-ttu-id="d38e2-190">このコマンドは、保留中の移行をデータベースに適用されます。</span><span class="sxs-lookup"><span data-stu-id="d38e2-190">This command will apply any pending migrations to the database.</span></span> <span data-ttu-id="d38e2-191">InitialCreate 移行は、移行は、新しい AddUrl 移行のみ適用ために既に適用されています。</span><span class="sxs-lookup"><span data-stu-id="d38e2-191">Our InitialCreate migration has already been applied so migrations will just apply our new AddUrl migration.</span></span>
    <span data-ttu-id="d38e2-192">ヒント: が使用できる、 **– Verbose**データベースに対して実行されている SQL を表示するには、Update-database を呼び出すときに切り替えます。</span><span class="sxs-lookup"><span data-stu-id="d38e2-192">Tip: You can use the **–Verbose** switch when calling Update-Database to see the SQL that is being executed against the database.</span></span>

<span data-ttu-id="d38e2-193">データベースのブログ テーブルには、新しい Url 列は追加がようになりました。</span><span class="sxs-lookup"><span data-stu-id="d38e2-193">The new Url column is now added to the Blogs table in the database:</span></span>

![Url のスキーマ](~/ef6/media/schemawithurl.png)

## <a name="6-data-annotations"></a><span data-ttu-id="d38e2-195">6.データの注釈</span><span class="sxs-lookup"><span data-stu-id="d38e2-195">6. Data Annotations</span></span>

<span data-ttu-id="d38e2-196">だけした EF の既定の規約を使用して、モデルを検出できるようにこれまでがあるとなる時間とクラスは、規則に従っていないし、詳細な構成を実行できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="d38e2-196">So far we’ve just let EF discover the model using its default conventions, but there are going to be times when our classes don’t follow the conventions and we need to be able to perform further configuration.</span></span> <span data-ttu-id="d38e2-197">は、この 2 つのオプションがあります。このセクションではデータ注釈とし、次のセクションで、fluent API に注目します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-197">There are two options for this; we’ll look at Data Annotations in this section and then the fluent API in the next section.</span></span>

-   <span data-ttu-id="d38e2-198">私たちのモデルに追加するユーザー クラス</span><span class="sxs-lookup"><span data-stu-id="d38e2-198">Let’s add a User class to our model</span></span>

``` csharp
public class User
{
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   <span data-ttu-id="d38e2-199">また、派生コンテキストにセットを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d38e2-199">We also need to add a set to our derived context</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }
}
```

-   <span data-ttu-id="d38e2-200">というエラー メッセージがこのように移行を追加しようとすると"*'User' の EntityType にキーが定義されていません。この EntityType にキーを定義します。"*</span><span class="sxs-lookup"><span data-stu-id="d38e2-200">If we tried to add a migration we’d get an error saying “*EntityType ‘User’ has no key defined. Define the key for this EntityType.”*</span></span> <span data-ttu-id="d38e2-201">EF には、ユーザー名のユーザーのプライマリ キーがあることを知る方法がありません。</span><span class="sxs-lookup"><span data-stu-id="d38e2-201">because EF has no way of knowing that Username should be the primary key for User.</span></span>
-   <span data-ttu-id="d38e2-202">今すぐデータ注釈を使用して、使用して、追加する必要がありますを Program.cs の上部にあるステートメント</span><span class="sxs-lookup"><span data-stu-id="d38e2-202">We’re going to use Data Annotations now so we need to add a using statement at the top of Program.cs</span></span>

```
using System.ComponentModel.DataAnnotations;
```

-   <span data-ttu-id="d38e2-203">主キーであるかを識別するためにユーザー名プロパティに注釈を付けるようになりました</span><span class="sxs-lookup"><span data-stu-id="d38e2-203">Now annotate the Username property to identify that it is the primary key</span></span>

``` csharp
public class User
{
    [Key]
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   <span data-ttu-id="d38e2-204">使用して、 **Add-migration AddUser**データベースに変更を適用これらへの移行をスキャフォールディングするコマンド</span><span class="sxs-lookup"><span data-stu-id="d38e2-204">Use the **Add-Migration AddUser** command to scaffold a migration to apply these changes to the database</span></span>
-   <span data-ttu-id="d38e2-205">実行、 **Update-database**データベースに新しい移行を適用するコマンド</span><span class="sxs-lookup"><span data-stu-id="d38e2-205">Run the **Update-Database** command to apply the new migration to the database</span></span>

<span data-ttu-id="d38e2-206">新しいテーブルがデータベースに追加ようになりました。</span><span class="sxs-lookup"><span data-stu-id="d38e2-206">The new table is now added to the database:</span></span>

![ユーザーとスキーマ](~/ef6/media/schemawithusers.png)

<span data-ttu-id="d38e2-208">EF によってサポートされている注釈の完全な一覧です。</span><span class="sxs-lookup"><span data-stu-id="d38e2-208">The full list of annotations supported by EF is:</span></span>

-   [<span data-ttu-id="d38e2-209">KeyAttribute</span><span class="sxs-lookup"><span data-stu-id="d38e2-209">KeyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.keyattribute)
-   [<span data-ttu-id="d38e2-210">StringLengthAttribute</span><span class="sxs-lookup"><span data-stu-id="d38e2-210">StringLengthAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute)
-   [<span data-ttu-id="d38e2-211">MaxLengthAttribute</span><span class="sxs-lookup"><span data-stu-id="d38e2-211">MaxLengthAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.maxlengthattribute)
-   [<span data-ttu-id="d38e2-212">ConcurrencyCheckAttribute</span><span class="sxs-lookup"><span data-stu-id="d38e2-212">ConcurrencyCheckAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.concurrencycheckattribute)
-   [<span data-ttu-id="d38e2-213">RequiredAttribute</span><span class="sxs-lookup"><span data-stu-id="d38e2-213">RequiredAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute)
-   [<span data-ttu-id="d38e2-214">TimestampAttribute</span><span class="sxs-lookup"><span data-stu-id="d38e2-214">TimestampAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute)
-   [<span data-ttu-id="d38e2-215">ComplexTypeAttribute</span><span class="sxs-lookup"><span data-stu-id="d38e2-215">ComplexTypeAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.complextypeattribute)
-   [<span data-ttu-id="d38e2-216">ColumnAttribute</span><span class="sxs-lookup"><span data-stu-id="d38e2-216">ColumnAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.columnattribute)
-   [<span data-ttu-id="d38e2-217">TableAttribute</span><span class="sxs-lookup"><span data-stu-id="d38e2-217">TableAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.tableattribute)
-   [<span data-ttu-id="d38e2-218">InversePropertyAttribute</span><span class="sxs-lookup"><span data-stu-id="d38e2-218">InversePropertyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.inversepropertyattribute)
-   [<span data-ttu-id="d38e2-219">ForeignKeyAttribute</span><span class="sxs-lookup"><span data-stu-id="d38e2-219">ForeignKeyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.foreignkeyattribute)
-   [<span data-ttu-id="d38e2-220">DatabaseGeneratedAttribute</span><span class="sxs-lookup"><span data-stu-id="d38e2-220">DatabaseGeneratedAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedattribute)
-   [<span data-ttu-id="d38e2-221">NotMappedAttribute</span><span class="sxs-lookup"><span data-stu-id="d38e2-221">NotMappedAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.notmappedattribute)

## <a name="7-fluent-api"></a><span data-ttu-id="d38e2-222">7.Fluent API</span><span class="sxs-lookup"><span data-stu-id="d38e2-222">7. Fluent API</span></span>

<span data-ttu-id="d38e2-223">前のセクションでを補完したり、規則によって検出されたものをオーバーライドするデータの注釈の使用について説明しました。</span><span class="sxs-lookup"><span data-stu-id="d38e2-223">In the previous section we looked at using Data Annotations to supplement or override what was detected by convention.</span></span> <span data-ttu-id="d38e2-224">モデルを構成するその他の方法は、Code First fluent API を使用してです。</span><span class="sxs-lookup"><span data-stu-id="d38e2-224">The other way to configure the model is via the Code First fluent API.</span></span>

<span data-ttu-id="d38e2-225">ほとんどのモデル構成には単純なデータの注釈を使用します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-225">Most model configuration can be done using simple data annotations.</span></span> <span data-ttu-id="d38e2-226">Fluent API は、データ注釈は、データ注釈では実現できないいくつかのより高度な構成をさらに実行できる情報を網羅したモデルの構成を指定するより高度な方法です。</span><span class="sxs-lookup"><span data-stu-id="d38e2-226">The fluent API is a more advanced way of specifying model configuration that covers everything that data annotations can do in addition to some more advanced configuration not possible with data annotations.</span></span> <span data-ttu-id="d38e2-227">データ注釈と fluent API を一緒に使用することができます。</span><span class="sxs-lookup"><span data-stu-id="d38e2-227">Data annotations and the fluent API can be used together.</span></span>

<span data-ttu-id="d38e2-228">Fluent API にアクセスするには、DbContext で OnModelCreating メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="d38e2-228">To access the fluent API you override the OnModelCreating method in DbContext.</span></span> <span data-ttu-id="d38e2-229">表示するに User.DisplayName が格納されている列の名前を変更したいと\_名。</span><span class="sxs-lookup"><span data-stu-id="d38e2-229">Let’s say we wanted to rename the column that User.DisplayName is stored in to display\_name.</span></span>

-   <span data-ttu-id="d38e2-230">次のコードで BloggingContext で OnModelCreating メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="d38e2-230">Override the OnModelCreating method on BloggingContext with the following code</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Entity<User>()
            .Property(u => u.DisplayName)
            .HasColumnName("display_name");
    }
}
```

-   <span data-ttu-id="d38e2-231">使用して、 **Add-migration ChangeDisplayName**コマンドは、これらを適用する移行をスキャフォールディングするデータベースを変更します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-231">Use the **Add-Migration ChangeDisplayName** command to scaffold a migration to apply these changes to the database.</span></span>
-   <span data-ttu-id="d38e2-232">実行、 **Update-database**データベースに新しい移行を適用するコマンド。</span><span class="sxs-lookup"><span data-stu-id="d38e2-232">Run the **Update-Database** command to apply the new migration to the database.</span></span>

<span data-ttu-id="d38e2-233">DisplayName 列の名前が表示にようになりました\_名。</span><span class="sxs-lookup"><span data-stu-id="d38e2-233">The DisplayName column is now renamed to display\_name:</span></span>

![スキーマの名前を変更する表示名](~/ef6/media/schemawithdisplaynamerenamed.png)

## <a name="summary"></a><span data-ttu-id="d38e2-235">まとめ</span><span class="sxs-lookup"><span data-stu-id="d38e2-235">Summary</span></span>

<span data-ttu-id="d38e2-236">このチュートリアルでは、新しいデータベースを使用して Code First の開発に説明しました。</span><span class="sxs-lookup"><span data-stu-id="d38e2-236">In this walkthrough we looked at Code First development using a new database.</span></span> <span data-ttu-id="d38e2-237">私たちは、クラスを使用してモデルを定義し、そのモデル データベースを作成し、格納およびデータを取得するために使用します。</span><span class="sxs-lookup"><span data-stu-id="d38e2-237">We defined a model using classes then used that model to create a database and store and retrieve data.</span></span> <span data-ttu-id="d38e2-238">データベースが作成された後 Code First Migrations を使用して、進化、モデルとスキーマを変更しました。</span><span class="sxs-lookup"><span data-stu-id="d38e2-238">Once the database was created we used Code First Migrations to change the schema as our model evolved.</span></span> <span data-ttu-id="d38e2-239">また、データ注釈と Fluent API を使用してモデルを構成する方法を説明しました。</span><span class="sxs-lookup"><span data-stu-id="d38e2-239">We also saw how to configure a model using Data Annotations and the Fluent API.</span></span>
