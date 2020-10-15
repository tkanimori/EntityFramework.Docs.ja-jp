---
title: 新しいデータベースへの Code First-EF6
description: Entity Framework 6 の新しいデータベースへの Code First
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/workflows/new-database
ms.openlocfilehash: 9d0082ac5226fff066d3e18c9164e2230c84b285
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065020"
---
# <a name="code-first-to-a-new-database"></a><span data-ttu-id="9eb10-103">新しいデータベースの Code First</span><span class="sxs-lookup"><span data-stu-id="9eb10-103">Code First to a New Database</span></span>
<span data-ttu-id="9eb10-104">このビデオとステップバイステップのチュートリアルでは、新しいデータベースを対象とした Code First 開発の概要について説明します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-104">This video and step-by-step walkthrough provide an introduction to Code First development targeting a new database.</span></span> <span data-ttu-id="9eb10-105">このシナリオには、存在しないデータベースのターゲット設定、Code First 作成されるデータベース、または Code First が新しいテーブルを追加する空のデータベースが対象となります。</span><span class="sxs-lookup"><span data-stu-id="9eb10-105">This scenario includes targeting a database that doesn’t exist and Code First will create, or an empty database that Code First will add new tables to.</span></span> <span data-ttu-id="9eb10-106">Code First では、C または VB.Net クラスを使用してモデルを定義でき \# ます。</span><span class="sxs-lookup"><span data-stu-id="9eb10-106">Code First allows you to define your model using C\# or VB.Net classes.</span></span> <span data-ttu-id="9eb10-107">追加の構成は、必要に応じて、クラスやプロパティの属性、または fluent API を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="9eb10-107">Additional configuration can optionally be performed using attributes on your classes and properties or by using a fluent API.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="9eb10-108">ビデオを見る</span><span class="sxs-lookup"><span data-stu-id="9eb10-108">Watch the video</span></span>
<span data-ttu-id="9eb10-109">このビデオでは、新しいデータベースを対象とした Code First 開発の概要について説明します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-109">This video provides an introduction to Code First development targeting a new database.</span></span> <span data-ttu-id="9eb10-110">このシナリオには、存在しないデータベースのターゲット設定、Code First 作成されるデータベース、または Code First が新しいテーブルを追加する空のデータベースが対象となります。</span><span class="sxs-lookup"><span data-stu-id="9eb10-110">This scenario includes targeting a database that doesn’t exist and Code First will create, or an empty database that Code First will add new tables to.</span></span> <span data-ttu-id="9eb10-111">Code First を使用すると、C# または VB.Net クラスを使用してモデルを定義できます。</span><span class="sxs-lookup"><span data-stu-id="9eb10-111">Code First allows you to define your model using C# or VB.Net classes.</span></span> <span data-ttu-id="9eb10-112">追加の構成は、必要に応じて、クラスやプロパティの属性、または fluent API を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="9eb10-112">Additional configuration can optionally be performed using attributes on your classes and properties or by using a fluent API.</span></span>

<span data-ttu-id="9eb10-113">**提供**: [Rowan Miller](https://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="9eb10-113">**Presented By**: [Rowan Miller](https://romiller.com/)</span></span>

<span data-ttu-id="9eb10-114">**ビデオ**: [WMV](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv)  |  [MP4](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v)  |  [WMV (ZIP)](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)</span><span class="sxs-lookup"><span data-stu-id="9eb10-114">**Video**: [WMV](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv) | [MP4](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="9eb10-115">前提条件</span><span class="sxs-lookup"><span data-stu-id="9eb10-115">Pre-Requisites</span></span>

<span data-ttu-id="9eb10-116">このチュートリアルを完了するには、少なくとも Visual Studio 2010 または Visual Studio 2012 がインストールされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="9eb10-116">You will need to have at least Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="9eb10-117">Visual Studio 2010 を使用している場合は、 [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) もインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="9eb10-117">If you are using Visual Studio 2010, you will also need to have [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

## <a name="1-create-the-application"></a><span data-ttu-id="9eb10-118">1. アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="9eb10-118">1. Create the Application</span></span>

<span data-ttu-id="9eb10-119">単純にするために、Code First を使用してデータアクセスを実行する基本的なコンソールアプリケーションを構築します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-119">To keep things simple we’re going to build a basic console application that uses Code First to perform data access.</span></span>

-   <span data-ttu-id="9eb10-120">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="9eb10-120">Open Visual Studio</span></span>
-   <span data-ttu-id="9eb10-121">**ファイル- &gt; 新規 &gt; プロジェクト...**</span><span class="sxs-lookup"><span data-stu-id="9eb10-121">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="9eb10-122">左側のメニューと**コンソールアプリケーション**から [ **Windows** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-122">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="9eb10-123">名前として「 **Codefirstnewconnectionstring** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-123">Enter **CodeFirstNewDatabaseSample** as the name</span></span>
-   <span data-ttu-id="9eb10-124">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-124">Select **OK**</span></span>

## <a name="2-create-the-model"></a><span data-ttu-id="9eb10-125">2. モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="9eb10-125">2. Create the Model</span></span>

<span data-ttu-id="9eb10-126">クラスを使用して、非常に単純なモデルを定義してみましょう。</span><span class="sxs-lookup"><span data-stu-id="9eb10-126">Let’s define a very simple model using classes.</span></span> <span data-ttu-id="9eb10-127">これらは Program.cs ファイルで定義するだけですが、実際のアプリケーションでは、クラスを別々のファイルに分割し、場合によっては別のプロジェクトに分割します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-127">We’re just defining them in the Program.cs file but in a real world application you would split your classes out into separate files and potentially a separate project.</span></span>

<span data-ttu-id="9eb10-128">Program.cs の Program クラス定義の下に、次の2つのクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-128">Below the Program class definition in Program.cs add the following two classes.</span></span>

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

<span data-ttu-id="9eb10-129">2つのナビゲーションプロパティ (ブログ、投稿、ブログ) が作成されていることがわかります。</span><span class="sxs-lookup"><span data-stu-id="9eb10-129">You’ll notice that we’re making the two navigation properties (Blog.Posts and Post.Blog) virtual.</span></span> <span data-ttu-id="9eb10-130">これにより、Entity Framework の遅延読み込み機能が有効になります。</span><span class="sxs-lookup"><span data-stu-id="9eb10-130">This enables the Lazy Loading feature of Entity Framework.</span></span> <span data-ttu-id="9eb10-131">遅延読み込みとは、これらのプロパティにアクセスしようとしたときに、それらのプロパティの内容がデータベースから自動的に読み込まれることを意味します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-131">Lazy Loading means that the contents of these properties will be automatically loaded from the database when you try to access them.</span></span>

## <a name="3-create-a-context"></a><span data-ttu-id="9eb10-132">3. コンテキストを作成する</span><span class="sxs-lookup"><span data-stu-id="9eb10-132">3. Create a Context</span></span>

<span data-ttu-id="9eb10-133">次に、データベースとのセッションを表す派生コンテキストを定義します。これにより、データのクエリと保存が可能になります。</span><span class="sxs-lookup"><span data-stu-id="9eb10-133">Now it’s time to define a derived context, which represents a session with the database, allowing us to query and save data.</span></span> <span data-ttu-id="9eb10-134">ここでは、TEntity から派生し、 &lt; &gt; モデル内のクラスごとに型指定された dbcontext を公開するコンテキストを定義します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-134">We define a context that derives from System.Data.Entity.DbContext and exposes a typed DbSet&lt;TEntity&gt; for each class in our model.</span></span>

<span data-ttu-id="9eb10-135">ここでは、Entity Framework の型の使用を開始しているため、EntityFramework NuGet パッケージを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9eb10-135">We’re now starting to use types from the Entity Framework so we need to add the EntityFramework NuGet package.</span></span>

-   <span data-ttu-id="9eb10-136">**プロジェクト– &gt; NuGet パッケージの管理...**</span><span class="sxs-lookup"><span data-stu-id="9eb10-136">**Project –&gt; Manage NuGet Packages…**</span></span>
    <span data-ttu-id="9eb10-137">注: [ **NuGet パッケージの管理...**</span><span class="sxs-lookup"><span data-stu-id="9eb10-137">Note: If you don’t have the **Manage NuGet Packages…**</span></span> <span data-ttu-id="9eb10-138">オプション[NuGet の最新バージョン](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)をインストールする必要があります</span><span class="sxs-lookup"><span data-stu-id="9eb10-138">option you should install the [latest version of NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)</span></span>
-   <span data-ttu-id="9eb10-139">[ **オンライン** ] タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-139">Select the **Online** tab</span></span>
-   <span data-ttu-id="9eb10-140">**Entityframework**パッケージを選択します</span><span class="sxs-lookup"><span data-stu-id="9eb10-140">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="9eb10-141">**[Install]** (インストール) をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9eb10-141">Click **Install**</span></span>

<span data-ttu-id="9eb10-142">Program.cs の先頭に、using ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-142">Add a using statement for System.Data.Entity at the top of Program.cs.</span></span>

``` csharp
using System.Data.Entity;
```

<span data-ttu-id="9eb10-143">Program.cs の Post クラスの下に、次の派生コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-143">Below the Post class in Program.cs add the following derived context.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```

<span data-ttu-id="9eb10-144">ここでは、Program.cs に含まれる内容の完全な一覧を示します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-144">Here is a complete listing of what Program.cs should now contain.</span></span>

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

<span data-ttu-id="9eb10-145">これは、データの格納と取得を開始するために必要なすべてのコードです。</span><span class="sxs-lookup"><span data-stu-id="9eb10-145">That is all the code we need to start storing and retrieving data.</span></span> <span data-ttu-id="9eb10-146">当然ながら、背後にはかなりの時間がかかりますが、それについては後で説明しますが、実際にはそれを実際に見てみましょう。</span><span class="sxs-lookup"><span data-stu-id="9eb10-146">Obviously there is quite a bit going on behind the scenes and we’ll take a look at that in a moment but first let’s see it in action.</span></span>

## <a name="4-reading--writing-data"></a><span data-ttu-id="9eb10-147">4. データの読み取り & 書き込み</span><span class="sxs-lookup"><span data-stu-id="9eb10-147">4. Reading & Writing Data</span></span>

<span data-ttu-id="9eb10-148">次に示すように、Program.cs に Main メソッドを実装します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-148">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="9eb10-149">このコードは、コンテキストの新しいインスタンスを作成し、それを使用して新しいブログを挿入します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-149">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="9eb10-150">次に、LINQ クエリを使用して、データベースのすべてのブログをタイトル別にアルファベット順に並べ替えて取得します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-150">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

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

<span data-ttu-id="9eb10-151">これで、アプリケーションを実行してテストできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="9eb10-151">You can now run the application and test it out.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
### <a name="wheres-my-data"></a><span data-ttu-id="9eb10-152">データはどこにありますか?</span><span class="sxs-lookup"><span data-stu-id="9eb10-152">Where’s My Data?</span></span>

<span data-ttu-id="9eb10-153">慣例により、DbContext によってデータベースが作成されました。</span><span class="sxs-lookup"><span data-stu-id="9eb10-153">By convention DbContext has created a database for you.</span></span>

-   <span data-ttu-id="9eb10-154">ローカルの SQL Express インスタンスが使用可能な場合 (既定では Visual Studio 2010 がインストールされている場合)、Code First によってそのインスタンスにデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9eb10-154">If a local SQL Express instance is available (installed by default with Visual Studio 2010) then Code First has created the database on that instance</span></span>
-   <span data-ttu-id="9eb10-155">SQL Express が使用できない場合、Code First は [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) を使用します (既定では Visual Studio 2012 と共にインストールされます)。</span><span class="sxs-lookup"><span data-stu-id="9eb10-155">If SQL Express isn’t available then Code First will try and use [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) (installed by default with Visual Studio 2012)</span></span>
-   <span data-ttu-id="9eb10-156">データベースには、派生コンテキストの完全修飾名の後に名前が付けられます。この例では、 **Codefirstnewbfile です。**</span><span class="sxs-lookup"><span data-stu-id="9eb10-156">The database is named after the fully qualified name of the derived context, in our case that is **CodeFirstNewDatabaseSample.BloggingContext**</span></span>

<span data-ttu-id="9eb10-157">これらは既定の規則であり、Code First 使用するデータベースを変更するにはさまざまな方法があります。詳細については、「 **DbContext がモデルとデータベースの接続を検出する方法** 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9eb10-157">These are just the default conventions and there are various ways to change the database that Code First uses, more information is available in the **How DbContext Discovers the Model and Database Connection** topic.</span></span>
<span data-ttu-id="9eb10-158">このデータベースに接続するには、Visual Studio のサーバーエクスプローラーを使用します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-158">You can connect to this database using Server Explorer in Visual Studio</span></span>

-   <span data-ttu-id="9eb10-159">**ビュー- &gt; サーバーエクスプローラー**</span><span class="sxs-lookup"><span data-stu-id="9eb10-159">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="9eb10-160">[**データ接続**] を右クリックし、[**接続の追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-160">Right click on **Data Connections** and select **Add Connection…**</span></span>
-   <span data-ttu-id="9eb10-161">サーバーエクスプローラーからデータベースに接続していない場合は、データソースとして Microsoft SQL Server を選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9eb10-161">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![データ ソースを選択する](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="9eb10-163">インストールされているものに応じて、LocalDB または SQL Express に接続します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-163">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>

<span data-ttu-id="9eb10-164">Code First 作成されたスキーマを調査できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="9eb10-164">We can now inspect the schema that Code First created.</span></span>

![スキーマの初期](~/ef6/media/schemainitial.png)

<span data-ttu-id="9eb10-166">DbContext は、定義した Dbcontext プロパティを参照して、モデルに含めるクラスを処理しました。</span><span class="sxs-lookup"><span data-stu-id="9eb10-166">DbContext worked out what classes to include in the model by looking at the DbSet properties that we defined.</span></span> <span data-ttu-id="9eb10-167">次に、Code First 規則の既定のセットを使用して、テーブル名と列名の決定、データ型の確認、主キーの検索などを行います。</span><span class="sxs-lookup"><span data-stu-id="9eb10-167">It then uses the default set of Code First conventions to determine table and column names, determine data types, find primary keys, etc.</span></span> <span data-ttu-id="9eb10-168">このチュートリアルの後半では、これらの規則をオーバーライドする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-168">Later in this walkthrough we’ll look at how you can override these conventions.</span></span>

## <a name="5-dealing-with-model-changes"></a><span data-ttu-id="9eb10-169">5. モデルの変更を処理する</span><span class="sxs-lookup"><span data-stu-id="9eb10-169">5. Dealing with Model Changes</span></span>

<span data-ttu-id="9eb10-170">次に、モデルに何らかの変更を加えてみましょう。これらの変更を行うときは、データベーススキーマも更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9eb10-170">Now it’s time to make some changes to our model, when we make these changes we also need to update the database schema.</span></span> <span data-ttu-id="9eb10-171">これを行うには、Code First Migrations、または短い間の移行と呼ばれる機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-171">To do this we are going to use a feature called Code First Migrations, or Migrations for short.</span></span>

<span data-ttu-id="9eb10-172">移行により、データベーススキーマをアップグレード (およびダウングレード) する方法を説明する一連の手順を実行できます。</span><span class="sxs-lookup"><span data-stu-id="9eb10-172">Migrations allows us to have an ordered set of steps that describe how to upgrade (and downgrade) our database schema.</span></span> <span data-ttu-id="9eb10-173">これらの各手順 (移行と呼ばれます) には、適用される変更を説明するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9eb10-173">Each of these steps, known as a migration, contains some code that describes the changes to be applied.</span></span> 

<span data-ttu-id="9eb10-174">最初の手順では、BCode First Migrations コンテキストのを有効にします。</span><span class="sxs-lookup"><span data-stu-id="9eb10-174">The first step is to enable Code First Migrations for our BloggingContext.</span></span>

-   <span data-ttu-id="9eb10-175">**ツール- &gt; ライブラリパッケージマネージャー- &gt; パッケージマネージャーコンソール**</span><span class="sxs-lookup"><span data-stu-id="9eb10-175">**Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
-   <span data-ttu-id="9eb10-176">パッケージ マネージャー コンソールで **Enable-Migrations** コマンドを実行します</span><span class="sxs-lookup"><span data-stu-id="9eb10-176">Run the **Enable-Migrations** command in Package Manager Console</span></span>
-   <span data-ttu-id="9eb10-177">次の2つの項目を含む新しい [移行] フォルダーがプロジェクトに追加されました。</span><span class="sxs-lookup"><span data-stu-id="9eb10-177">A new Migrations folder has been added to our project that contains two items:</span></span>
    -   <span data-ttu-id="9eb10-178">**Configuration.cs** –このファイルには、b コンテキストの移行に使用する設定が含まれています。</span><span class="sxs-lookup"><span data-stu-id="9eb10-178">**Configuration.cs** – This file contains the settings that Migrations will use for migrating BloggingContext.</span></span> <span data-ttu-id="9eb10-179">このチュートリアルでは何も変更する必要はありませんが、ここでは、シードデータを指定したり、他のデータベースのプロバイダーを登録したり、移行が生成される名前空間を変更したりできます。</span><span class="sxs-lookup"><span data-stu-id="9eb10-179">We don’t need to change anything for this walkthrough, but here is where you can specify seed data, register providers for other databases, changes the namespace that migrations are generated in etc.</span></span>
    -   <span data-ttu-id="9eb10-180">\*\* &lt; timestamp &gt; \_ InitialCreate.cs\*\* –これは最初の移行です。これは、データベースに既に適用されている変更を表し、ブログと投稿テーブルを含むデータベースに対して空のデータベースからの変更を行います。</span><span class="sxs-lookup"><span data-stu-id="9eb10-180">**&lt;timestamp&gt;\_InitialCreate.cs** – This is your first migration, it represents the changes that have already been applied to the database to take it from being an empty database to one that includes the Blogs and Posts tables.</span></span> <span data-ttu-id="9eb10-181">これらのテーブルは Code First によって自動的に作成されるようになっていますが、移行に移行された移行を選択しました。</span><span class="sxs-lookup"><span data-stu-id="9eb10-181">Although we let Code First automatically create these tables for us, now that we have opted in to Migrations they have been converted into a Migration.</span></span> <span data-ttu-id="9eb10-182">Code First は、この移行が既に適用されているローカルデータベースにも記録されています。</span><span class="sxs-lookup"><span data-stu-id="9eb10-182">Code First has also recorded in our local database that this Migration has already been applied.</span></span> <span data-ttu-id="9eb10-183">ファイル名のタイムスタンプは、順序付けの目的で使用されます。</span><span class="sxs-lookup"><span data-stu-id="9eb10-183">The timestamp on the filename is used for ordering purposes.</span></span>

    <span data-ttu-id="9eb10-184">次に、モデルに変更を加え、ブログクラスに Url プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-184">Now let’s make a change to our model, add a Url property to the Blog class:</span></span>

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }

    public virtual List<Post> Posts { get; set; }
}
```

-   <span data-ttu-id="9eb10-185">パッケージマネージャーコンソールで、[ **追加-移行] AddUrl** コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-185">Run the **Add-Migration AddUrl** command in Package Manager Console.</span></span>
    <span data-ttu-id="9eb10-186">Add-Migration コマンドは、前回の移行以降に行われた変更を確認し、見つかった変更を含む新しい移行をスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="9eb10-186">The Add-Migration command checks for changes since your last migration and scaffolds a new migration with any changes that are found.</span></span> <span data-ttu-id="9eb10-187">移行に名前を付けることができます。この例では、移行 ' AddUrl ' を呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="9eb10-187">We can give migrations a name; in this case we are calling the migration ‘AddUrl’.</span></span>
    <span data-ttu-id="9eb10-188">スキャフォールディングコードは、文字列データを保持できる Url 列を dbo に追加する必要があることを示しています。ブログテーブル。</span><span class="sxs-lookup"><span data-stu-id="9eb10-188">The scaffolded code is saying that we need to add a Url column, that can hold string data, to the dbo.Blogs table.</span></span> <span data-ttu-id="9eb10-189">必要に応じて、スキャフォールディングコードを編集することもできますが、この場合は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="9eb10-189">If needed, we could edit the scaffolded code but that’s not required in this case.</span></span>

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

-   <span data-ttu-id="9eb10-190">パッケージマネージャーコンソールで、 **データベースの更新** コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-190">Run the **Update-Database** command in Package Manager Console.</span></span> <span data-ttu-id="9eb10-191">このコマンドを実行すると、保留中の移行がデータベースに適用されます。</span><span class="sxs-lookup"><span data-stu-id="9eb10-191">This command will apply any pending migrations to the database.</span></span> <span data-ttu-id="9eb10-192">InitialCreate 移行は既に適用されているので、移行によって新しい AddUrl 移行が適用されます。</span><span class="sxs-lookup"><span data-stu-id="9eb10-192">Our InitialCreate migration has already been applied so migrations will just apply our new AddUrl migration.</span></span>
    <span data-ttu-id="9eb10-193">ヒント: Update-Database を呼び出すときに **– Verbose** スイッチを使用して、データベースに対して実行される SQL を確認できます。</span><span class="sxs-lookup"><span data-stu-id="9eb10-193">Tip: You can use the **–Verbose** switch when calling Update-Database to see the SQL that is being executed against the database.</span></span>

<span data-ttu-id="9eb10-194">これで、新しい Url 列がデータベースのブログテーブルに追加されました。</span><span class="sxs-lookup"><span data-stu-id="9eb10-194">The new Url column is now added to the Blogs table in the database:</span></span>

![Url を含むスキーマ](~/ef6/media/schemawithurl.png)

## <a name="6-data-annotations"></a><span data-ttu-id="9eb10-196">6. データ注釈</span><span class="sxs-lookup"><span data-stu-id="9eb10-196">6. Data Annotations</span></span>

<span data-ttu-id="9eb10-197">ここまでで、EF では既定の規則を使用してモデルを検出できるようになりましたが、クラスが規則に従わず、さらに構成を実行できるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="9eb10-197">So far we’ve just let EF discover the model using its default conventions, but there are going to be times when our classes don’t follow the conventions and we need to be able to perform further configuration.</span></span> <span data-ttu-id="9eb10-198">これには2つのオプションがあります。このセクションのデータ注釈については、次のセクションの fluent API を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9eb10-198">There are two options for this; we’ll look at Data Annotations in this section and then the fluent API in the next section.</span></span>

-   <span data-ttu-id="9eb10-199">モデルにユーザークラスを追加してみましょう。</span><span class="sxs-lookup"><span data-stu-id="9eb10-199">Let’s add a User class to our model</span></span>

``` csharp
public class User
{
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   <span data-ttu-id="9eb10-200">また、派生コンテキストにセットを追加する必要もあります。</span><span class="sxs-lookup"><span data-stu-id="9eb10-200">We also need to add a set to our derived context</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }
}
```

-   <span data-ttu-id="9eb10-201">移行を追加しようとすると、"*EntityType ' User ' にキーが定義されていません" というエラーが表示されます。この EntityType のキーを定義します。 "*</span><span class="sxs-lookup"><span data-stu-id="9eb10-201">If we tried to add a migration we’d get an error saying “*EntityType ‘User’ has no key defined. Define the key for this EntityType.”*</span></span> <span data-ttu-id="9eb10-202">EF では、ユーザー名がユーザーの主キーであることを把握する方法がありません。</span><span class="sxs-lookup"><span data-stu-id="9eb10-202">because EF has no way of knowing that Username should be the primary key for User.</span></span>
-   <span data-ttu-id="9eb10-203">ここではデータ注釈を使用します。そのため、Program.cs の先頭に using ステートメントを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9eb10-203">We’re going to use Data Annotations now so we need to add a using statement at the top of Program.cs</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
```

-   <span data-ttu-id="9eb10-204">次に、ユーザー名プロパティに注釈を付けて、それが主キーであることを識別します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-204">Now annotate the Username property to identify that it is the primary key</span></span>

``` csharp
public class User
{
    [Key]
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   <span data-ttu-id="9eb10-205">スキャフォールディングを使用 **して移行を実行し、** これらの変更をデータベースに適用します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-205">Use the **Add-Migration AddUser** command to scaffold a migration to apply these changes to the database</span></span>
-   <span data-ttu-id="9eb10-206">新しい移行をデータベースに適用するには、 **データベースの更新** コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-206">Run the **Update-Database** command to apply the new migration to the database</span></span>

<span data-ttu-id="9eb10-207">これで、新しいテーブルがデータベースに追加されました。</span><span class="sxs-lookup"><span data-stu-id="9eb10-207">The new table is now added to the database:</span></span>

![ユーザーを使用したスキーマ](~/ef6/media/schemawithusers.png)

<span data-ttu-id="9eb10-209">EF でサポートされている注釈の完全な一覧は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9eb10-209">The full list of annotations supported by EF is:</span></span>

-   [<span data-ttu-id="9eb10-210">KeyAttribute</span><span class="sxs-lookup"><span data-stu-id="9eb10-210">KeyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.keyattribute)
-   [<span data-ttu-id="9eb10-211">Stringlength 属性</span><span class="sxs-lookup"><span data-stu-id="9eb10-211">StringLengthAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute)
-   [<span data-ttu-id="9eb10-212">MaxLengthAttribute</span><span class="sxs-lookup"><span data-stu-id="9eb10-212">MaxLengthAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.maxlengthattribute)
-   [<span data-ttu-id="9eb10-213">ConcurrencyCheckAttribute</span><span class="sxs-lookup"><span data-stu-id="9eb10-213">ConcurrencyCheckAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.concurrencycheckattribute)
-   [<span data-ttu-id="9eb10-214">RequiredAttribute</span><span class="sxs-lookup"><span data-stu-id="9eb10-214">RequiredAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute)
-   [<span data-ttu-id="9eb10-215">タイムスタンプ属性</span><span class="sxs-lookup"><span data-stu-id="9eb10-215">TimestampAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute)
-   [<span data-ttu-id="9eb10-216">ComplexTypeAttribute</span><span class="sxs-lookup"><span data-stu-id="9eb10-216">ComplexTypeAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.complextypeattribute)
-   [<span data-ttu-id="9eb10-217">ColumnAttribute</span><span class="sxs-lookup"><span data-stu-id="9eb10-217">ColumnAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.columnattribute)
-   [<span data-ttu-id="9eb10-218">TableAttribute</span><span class="sxs-lookup"><span data-stu-id="9eb10-218">TableAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.tableattribute)
-   [<span data-ttu-id="9eb10-219">InversePropertyAttribute</span><span class="sxs-lookup"><span data-stu-id="9eb10-219">InversePropertyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.inversepropertyattribute)
-   [<span data-ttu-id="9eb10-220">ForeignKeyAttribute</span><span class="sxs-lookup"><span data-stu-id="9eb10-220">ForeignKeyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.foreignkeyattribute)
-   [<span data-ttu-id="9eb10-221">DatabaseGeneratedAttribute</span><span class="sxs-lookup"><span data-stu-id="9eb10-221">DatabaseGeneratedAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedattribute)
-   [<span data-ttu-id="9eb10-222">NotMappedAttribute</span><span class="sxs-lookup"><span data-stu-id="9eb10-222">NotMappedAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.notmappedattribute)

## <a name="7-fluent-api"></a><span data-ttu-id="9eb10-223">7. Fluent API</span><span class="sxs-lookup"><span data-stu-id="9eb10-223">7. Fluent API</span></span>

<span data-ttu-id="9eb10-224">前のセクションでは、データ注釈を使用して、規則によって検出された内容を補完またはオーバーライドする方法を説明しました。</span><span class="sxs-lookup"><span data-stu-id="9eb10-224">In the previous section we looked at using Data Annotations to supplement or override what was detected by convention.</span></span> <span data-ttu-id="9eb10-225">モデルを構成するもう1つの方法は、Code First fluent API を使用することです。</span><span class="sxs-lookup"><span data-stu-id="9eb10-225">The other way to configure the model is via the Code First fluent API.</span></span>

<span data-ttu-id="9eb10-226">ほとんどのモデル構成は、単純なデータ注釈を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="9eb10-226">Most model configuration can be done using simple data annotations.</span></span> <span data-ttu-id="9eb10-227">Fluent API は、データ注釈では実現できない高度な構成に加えて、データ注釈が実行できるすべてのものを網羅したモデル構成を指定するより高度な方法です。</span><span class="sxs-lookup"><span data-stu-id="9eb10-227">The fluent API is a more advanced way of specifying model configuration that covers everything that data annotations can do in addition to some more advanced configuration not possible with data annotations.</span></span> <span data-ttu-id="9eb10-228">データ注釈と fluent API を一緒に使用することができます。</span><span class="sxs-lookup"><span data-stu-id="9eb10-228">Data annotations and the fluent API can be used together.</span></span>

<span data-ttu-id="9eb10-229">Fluent API にアクセスするには、DbContext で OnModelCreating メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="9eb10-229">To access the fluent API you override the OnModelCreating method in DbContext.</span></span> <span data-ttu-id="9eb10-230">たとえば、ユーザー DisplayName が格納されている列の名前を表示名に変更したいとします \_ 。</span><span class="sxs-lookup"><span data-stu-id="9eb10-230">Let’s say we wanted to rename the column that User.DisplayName is stored in to display\_name.</span></span>

-   <span data-ttu-id="9eb10-231">次のコードを使用して、BloggingContext で OnModelCreating メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="9eb10-231">Override the OnModelCreating method on BloggingContext with the following code</span></span>

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

-   <span data-ttu-id="9eb10-232">スキャフォールディングを使用 **して移行を実行し、** これらの変更をデータベースに適用します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-232">Use the **Add-Migration ChangeDisplayName** command to scaffold a migration to apply these changes to the database.</span></span>
-   <span data-ttu-id="9eb10-233">新しい移行をデータベースに適用するには、 **データベースの更新** コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-233">Run the **Update-Database** command to apply the new migration to the database.</span></span>

<span data-ttu-id="9eb10-234">DisplayName 列の名前が表示名に変更されました \_ 。</span><span class="sxs-lookup"><span data-stu-id="9eb10-234">The DisplayName column is now renamed to display\_name:</span></span>

![表示名が変更されたスキーマ](~/ef6/media/schemawithdisplaynamerenamed.png)

## <a name="summary"></a><span data-ttu-id="9eb10-236">まとめ</span><span class="sxs-lookup"><span data-stu-id="9eb10-236">Summary</span></span>

<span data-ttu-id="9eb10-237">このチュートリアルでは、新しいデータベースを使用した Code First 開発について説明しました。</span><span class="sxs-lookup"><span data-stu-id="9eb10-237">In this walkthrough we looked at Code First development using a new database.</span></span> <span data-ttu-id="9eb10-238">クラスを使用してモデルを定義した後、そのモデルを使用してデータベースを作成し、データを格納および取得します。</span><span class="sxs-lookup"><span data-stu-id="9eb10-238">We defined a model using classes then used that model to create a database and store and retrieve data.</span></span> <span data-ttu-id="9eb10-239">データベースを作成した後は、モデルの進化に従ってスキーマを変更するために Code First Migrations を使用しました。</span><span class="sxs-lookup"><span data-stu-id="9eb10-239">Once the database was created we used Code First Migrations to change the schema as our model evolved.</span></span> <span data-ttu-id="9eb10-240">また、データ注釈と Fluent API を使用してモデルを構成する方法についても説明しました。</span><span class="sxs-lookup"><span data-stu-id="9eb10-240">We also saw how to configure a model using Data Annotations and the Fluent API.</span></span>
