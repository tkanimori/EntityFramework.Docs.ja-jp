---
title: 新しいデータベースへの Code First-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 2df6cb0a-7d8b-4e28-9d05-e2b9a90125af
ms.openlocfilehash: d540fc6e84049f345ae22998f94c309e0be73fc3
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182567"
---
# <a name="code-first-to-a-new-database"></a><span data-ttu-id="7bdde-102">新しいデータベースへの Code First</span><span class="sxs-lookup"><span data-stu-id="7bdde-102">Code First to a New Database</span></span>
<span data-ttu-id="7bdde-103">このビデオとステップバイステップのチュートリアルでは、新しいデータベースを対象とした Code First 開発の概要について説明します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-103">This video and step-by-step walkthrough provide an introduction to Code First development targeting a new database.</span></span> <span data-ttu-id="7bdde-104">このシナリオには、存在しないデータベースのターゲット設定、Code First 作成されるデータベース、または Code First が新しいテーブルを追加する空のデータベースが対象となります。</span><span class="sxs-lookup"><span data-stu-id="7bdde-104">This scenario includes targeting a database that doesn’t exist and Code First will create, or an empty database that Code First will add new tables to.</span></span> <span data-ttu-id="7bdde-105">Code First では、C\# または VB.Net クラスを使用してモデルを定義できます。</span><span class="sxs-lookup"><span data-stu-id="7bdde-105">Code First allows you to define your model using C\# or VB.Net classes.</span></span> <span data-ttu-id="7bdde-106">追加の構成は、必要に応じて、クラスやプロパティの属性、または fluent API を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="7bdde-106">Additional configuration can optionally be performed using attributes on your classes and properties or by using a fluent API.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="7bdde-107">ビデオを見る</span><span class="sxs-lookup"><span data-stu-id="7bdde-107">Watch the video</span></span>
<span data-ttu-id="7bdde-108">このビデオでは、新しいデータベースを対象とした Code First 開発の概要について説明します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-108">This video provides an introduction to Code First development targeting a new database.</span></span> <span data-ttu-id="7bdde-109">このシナリオには、存在しないデータベースのターゲット設定、Code First 作成されるデータベース、または Code First が新しいテーブルを追加する空のデータベースが対象となります。</span><span class="sxs-lookup"><span data-stu-id="7bdde-109">This scenario includes targeting a database that doesn’t exist and Code First will create, or an empty database that Code First will add new tables to.</span></span> <span data-ttu-id="7bdde-110">Code First では、または VB.Net クラスC#を使用してモデルを定義できます。</span><span class="sxs-lookup"><span data-stu-id="7bdde-110">Code First allows you to define your model using C# or VB.Net classes.</span></span> <span data-ttu-id="7bdde-111">追加の構成は、必要に応じて、クラスやプロパティの属性、または fluent API を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="7bdde-111">Additional configuration can optionally be performed using attributes on your classes and properties or by using a fluent API.</span></span>

<span data-ttu-id="7bdde-112">**提供**: [Rowan Miller](https://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="7bdde-112">**Presented By**: [Rowan Miller](https://romiller.com/)</span></span>

<span data-ttu-id="7bdde-113">**ビデオ**: [wmv](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv) | [MP4](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v) | [wmv (ZIP)](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)</span><span class="sxs-lookup"><span data-stu-id="7bdde-113">**Video**: [WMV](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv) | [MP4](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="7bdde-114">前提条件</span><span class="sxs-lookup"><span data-stu-id="7bdde-114">Pre-Requisites</span></span>

<span data-ttu-id="7bdde-115">このチュートリアルを完了するには、少なくとも Visual Studio 2010 または Visual Studio 2012 がインストールされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="7bdde-115">You will need to have at least Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="7bdde-116">Visual Studio 2010 を使用している場合は、 [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)もインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7bdde-116">If you are using Visual Studio 2010, you will also need to have [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

## <a name="1-create-the-application"></a><span data-ttu-id="7bdde-117">1. アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="7bdde-117">1. Create the Application</span></span>

<span data-ttu-id="7bdde-118">単純にするために、Code First を使用してデータアクセスを実行する基本的なコンソールアプリケーションを構築します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-118">To keep things simple we’re going to build a basic console application that uses Code First to perform data access.</span></span>

-   <span data-ttu-id="7bdde-119">Visual Studio を開きます</span><span class="sxs-lookup"><span data-stu-id="7bdde-119">Open Visual Studio</span></span>
-   <span data-ttu-id="7bdde-120">**ファイル&gt; 新規&gt; プロジェクト...**</span><span class="sxs-lookup"><span data-stu-id="7bdde-120">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="7bdde-121">左側のメニューと**コンソールアプリケーション**から **[Windows]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-121">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="7bdde-122">名前として「 **Codefirstnewconnectionstring** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-122">Enter **CodeFirstNewDatabaseSample** as the name</span></span>
-   <span data-ttu-id="7bdde-123">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-123">Select **OK**</span></span>

## <a name="2-create-the-model"></a><span data-ttu-id="7bdde-124">2. モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="7bdde-124">2. Create the Model</span></span>

<span data-ttu-id="7bdde-125">クラスを使用して、非常に単純なモデルを定義してみましょう。</span><span class="sxs-lookup"><span data-stu-id="7bdde-125">Let’s define a very simple model using classes.</span></span> <span data-ttu-id="7bdde-126">これらは Program.cs ファイルで定義するだけですが、実際のアプリケーションでは、クラスを別々のファイルに分割し、場合によっては別のプロジェクトに分割します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-126">We’re just defining them in the Program.cs file but in a real world application you would split your classes out into separate files and potentially a separate project.</span></span>

<span data-ttu-id="7bdde-127">Program.cs の Program クラス定義の下に、次の2つのクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-127">Below the Program class definition in Program.cs add the following two classes.</span></span>

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

<span data-ttu-id="7bdde-128">2つのナビゲーションプロパティ (ブログ、投稿、ブログ) が作成されていることがわかります。</span><span class="sxs-lookup"><span data-stu-id="7bdde-128">You’ll notice that we’re making the two navigation properties (Blog.Posts and Post.Blog) virtual.</span></span> <span data-ttu-id="7bdde-129">これにより、Entity Framework の遅延読み込み機能が有効になります。</span><span class="sxs-lookup"><span data-stu-id="7bdde-129">This enables the Lazy Loading feature of Entity Framework.</span></span> <span data-ttu-id="7bdde-130">遅延読み込みとは、これらのプロパティにアクセスしようとしたときに、それらのプロパティの内容がデータベースから自動的に読み込まれることを意味します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-130">Lazy Loading means that the contents of these properties will be automatically loaded from the database when you try to access them.</span></span>

## <a name="3-create-a-context"></a><span data-ttu-id="7bdde-131">3. コンテキストを作成する</span><span class="sxs-lookup"><span data-stu-id="7bdde-131">3. Create a Context</span></span>

<span data-ttu-id="7bdde-132">次に、データベースとのセッションを表す派生コンテキストを定義します。これにより、データのクエリと保存が可能になります。</span><span class="sxs-lookup"><span data-stu-id="7bdde-132">Now it’s time to define a derived context, which represents a session with the database, allowing us to query and save data.</span></span> <span data-ttu-id="7bdde-133">ここでは、TEntity から派生し、モデル内のクラスごとに型指定された Dbcontext&lt;&gt; を公開するコンテキストを定義します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-133">We define a context that derives from System.Data.Entity.DbContext and exposes a typed DbSet&lt;TEntity&gt; for each class in our model.</span></span>

<span data-ttu-id="7bdde-134">ここでは、Entity Framework の型の使用を開始しているため、EntityFramework NuGet パッケージを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7bdde-134">We’re now starting to use types from the Entity Framework so we need to add the EntityFramework NuGet package.</span></span>

-   <span data-ttu-id="7bdde-135">**プロジェクト–&gt; NuGet パッケージの管理...**</span><span class="sxs-lookup"><span data-stu-id="7bdde-135">**Project –&gt; Manage NuGet Packages…**</span></span>
    <span data-ttu-id="7bdde-136">注:  **NuGet パッケージの管理...**</span><span class="sxs-lookup"><span data-stu-id="7bdde-136">Note: If you don’t have the **Manage NuGet Packages…**</span></span> <span data-ttu-id="7bdde-137">オプション[NuGet の最新バージョン](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)をインストールする必要があります</span><span class="sxs-lookup"><span data-stu-id="7bdde-137">option you should install the [latest version of NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)</span></span>
-   <span data-ttu-id="7bdde-138">**[オンライン]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-138">Select the **Online** tab</span></span>
-   <span data-ttu-id="7bdde-139">**Entityframework**パッケージを選択します</span><span class="sxs-lookup"><span data-stu-id="7bdde-139">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="7bdde-140">**[インストール]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7bdde-140">Click **Install**</span></span>

<span data-ttu-id="7bdde-141">Program.cs の先頭に、using ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-141">Add a using statement for System.Data.Entity at the top of Program.cs.</span></span>

``` csharp
using System.Data.Entity;
```

<span data-ttu-id="7bdde-142">Program.cs の Post クラスの下に、次の派生コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-142">Below the Post class in Program.cs add the following derived context.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```

<span data-ttu-id="7bdde-143">ここでは、Program.cs に含まれる内容の完全な一覧を示します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-143">Here is a complete listing of what Program.cs should now contain.</span></span>

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

<span data-ttu-id="7bdde-144">これは、データの格納と取得を開始するために必要なすべてのコードです。</span><span class="sxs-lookup"><span data-stu-id="7bdde-144">That is all the code we need to start storing and retrieving data.</span></span> <span data-ttu-id="7bdde-145">当然ながら、背後にはかなりの時間がかかりますが、それについては後で説明しますが、実際にはそれを実際に見てみましょう。</span><span class="sxs-lookup"><span data-stu-id="7bdde-145">Obviously there is quite a bit going on behind the scenes and we’ll take a look at that in a moment but first let’s see it in action.</span></span>

## <a name="4-reading--writing-data"></a><span data-ttu-id="7bdde-146">4. データの読み取り & 書き込み</span><span class="sxs-lookup"><span data-stu-id="7bdde-146">4. Reading & Writing Data</span></span>

<span data-ttu-id="7bdde-147">次に示すように、Program.cs に Main メソッドを実装します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-147">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="7bdde-148">このコードは、コンテキストの新しいインスタンスを作成し、それを使用して新しいブログを挿入します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-148">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="7bdde-149">次に、LINQ クエリを使用して、データベースのすべてのブログをタイトル別にアルファベット順に並べ替えて取得します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-149">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

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

<span data-ttu-id="7bdde-150">これで、アプリケーションを実行してテストできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="7bdde-150">You can now run the application and test it out.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
### <a name="wheres-my-data"></a><span data-ttu-id="7bdde-151">データはどこにありますか?</span><span class="sxs-lookup"><span data-stu-id="7bdde-151">Where’s My Data?</span></span>

<span data-ttu-id="7bdde-152">慣例により、DbContext によってデータベースが作成されました。</span><span class="sxs-lookup"><span data-stu-id="7bdde-152">By convention DbContext has created a database for you.</span></span>

-   <span data-ttu-id="7bdde-153">ローカルの SQL Express インスタンスが使用可能な場合 (既定では Visual Studio 2010 がインストールされている場合)、Code First によってそのインスタンスにデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="7bdde-153">If a local SQL Express instance is available (installed by default with Visual Studio 2010) then Code First has created the database on that instance</span></span>
-   <span data-ttu-id="7bdde-154">SQL Express が使用できない場合、Code First は[LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx)を使用します (既定では Visual Studio 2012 と共にインストールされます)。</span><span class="sxs-lookup"><span data-stu-id="7bdde-154">If SQL Express isn’t available then Code First will try and use [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) (installed by default with Visual Studio 2012)</span></span>
-   <span data-ttu-id="7bdde-155">データベースには、派生コンテキストの完全修飾名の後に名前が付けられます。この例では、 **Codefirstnewbfile です。**</span><span class="sxs-lookup"><span data-stu-id="7bdde-155">The database is named after the fully qualified name of the derived context, in our case that is **CodeFirstNewDatabaseSample.BloggingContext**</span></span>

<span data-ttu-id="7bdde-156">これらは既定の規則であり、Code First 使用するデータベースを変更するにはさまざまな方法があります。詳細については、「 **DbContext がモデルとデータベースの接続を検出する方法**」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7bdde-156">These are just the default conventions and there are various ways to change the database that Code First uses, more information is available in the **How DbContext Discovers the Model and Database Connection** topic.</span></span>
<span data-ttu-id="7bdde-157">このデータベースに接続するには、Visual Studio のサーバーエクスプローラーを使用します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-157">You can connect to this database using Server Explorer in Visual Studio</span></span>

-   <span data-ttu-id="7bdde-158">**ビュー&gt; サーバーエクスプローラー**</span><span class="sxs-lookup"><span data-stu-id="7bdde-158">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="7bdde-159">**[データ接続]** を右クリックし、 **[接続の追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-159">Right click on **Data Connections** and select **Add Connection…**</span></span>
-   <span data-ttu-id="7bdde-160">サーバーエクスプローラーからデータベースに接続していない場合は、データソースとして Microsoft SQL Server を選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7bdde-160">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![データ ソースの選択](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="7bdde-162">インストールされているものに応じて、LocalDB または SQL Express に接続します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-162">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>

<span data-ttu-id="7bdde-163">Code First 作成されたスキーマを調査できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="7bdde-163">We can now inspect the schema that Code First created.</span></span>

![スキーマの初期](~/ef6/media/schemainitial.png)

<span data-ttu-id="7bdde-165">DbContext は、定義した Dbcontext プロパティを参照して、モデルに含めるクラスを処理しました。</span><span class="sxs-lookup"><span data-stu-id="7bdde-165">DbContext worked out what classes to include in the model by looking at the DbSet properties that we defined.</span></span> <span data-ttu-id="7bdde-166">次に、Code First 規則の既定のセットを使用して、テーブル名と列名の決定、データ型の確認、主キーの検索などを行います。</span><span class="sxs-lookup"><span data-stu-id="7bdde-166">It then uses the default set of Code First conventions to determine table and column names, determine data types, find primary keys, etc.</span></span> <span data-ttu-id="7bdde-167">このチュートリアルの後半では、これらの規則をオーバーライドする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-167">Later in this walkthrough we’ll look at how you can override these conventions.</span></span>

## <a name="5-dealing-with-model-changes"></a><span data-ttu-id="7bdde-168">5. モデルの変更を処理する</span><span class="sxs-lookup"><span data-stu-id="7bdde-168">5. Dealing with Model Changes</span></span>

<span data-ttu-id="7bdde-169">次に、モデルに何らかの変更を加えてみましょう。これらの変更を行うときは、データベーススキーマも更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7bdde-169">Now it’s time to make some changes to our model, when we make these changes we also need to update the database schema.</span></span> <span data-ttu-id="7bdde-170">これを行うには、Code First Migrations、または短い間の移行と呼ばれる機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-170">To do this we are going to use a feature called Code First Migrations, or Migrations for short.</span></span>

<span data-ttu-id="7bdde-171">移行により、データベーススキーマをアップグレード (およびダウングレード) する方法を説明する一連の手順を実行できます。</span><span class="sxs-lookup"><span data-stu-id="7bdde-171">Migrations allows us to have an ordered set of steps that describe how to upgrade (and downgrade) our database schema.</span></span> <span data-ttu-id="7bdde-172">これらの各手順 (移行と呼ばれます) には、適用される変更を説明するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7bdde-172">Each of these steps, known as a migration, contains some code that describes the changes to be applied.</span></span> 

<span data-ttu-id="7bdde-173">最初の手順では、BCode First Migrations コンテキストのを有効にします。</span><span class="sxs-lookup"><span data-stu-id="7bdde-173">The first step is to enable Code First Migrations for our BloggingContext.</span></span>

-   <span data-ttu-id="7bdde-174">**ツール-&gt; ライブラリパッケージマネージャー-&gt; パッケージマネージャーコンソール**</span><span class="sxs-lookup"><span data-stu-id="7bdde-174">**Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
-   <span data-ttu-id="7bdde-175">パッケージ マネージャー コンソールで **Enable-Migrations** コマンドを実行します</span><span class="sxs-lookup"><span data-stu-id="7bdde-175">Run the **Enable-Migrations** command in Package Manager Console</span></span>
-   <span data-ttu-id="7bdde-176">次の2つの項目を含む新しい [移行] フォルダーがプロジェクトに追加されました。</span><span class="sxs-lookup"><span data-stu-id="7bdde-176">A new Migrations folder has been added to our project that contains two items:</span></span>
    -   <span data-ttu-id="7bdde-177">**Configuration.cs** –このファイルには、b コンテキストの移行に使用する設定が含まれています。</span><span class="sxs-lookup"><span data-stu-id="7bdde-177">**Configuration.cs** – This file contains the settings that Migrations will use for migrating BloggingContext.</span></span> <span data-ttu-id="7bdde-178">このチュートリアルでは何も変更する必要はありませんが、ここでは、シードデータを指定したり、他のデータベースのプロバイダーを登録したり、移行が生成される名前空間を変更したりできます。</span><span class="sxs-lookup"><span data-stu-id="7bdde-178">We don’t need to change anything for this walkthrough, but here is where you can specify seed data, register providers for other databases, changes the namespace that migrations are generated in etc.</span></span>
    -   <span data-ttu-id="7bdde-179">**&lt;タイムスタンプ&gt;\_InitialCreate.cs** –これは最初の移行です。これは、データベースに既に適用されている変更を表します。この変更は、ブログおよび投稿テーブルを含む空のデータベースにするためのものです。</span><span class="sxs-lookup"><span data-stu-id="7bdde-179">**&lt;timestamp&gt;\_InitialCreate.cs** – This is your first migration, it represents the changes that have already been applied to the database to take it from being an empty database to one that includes the Blogs and Posts tables.</span></span> <span data-ttu-id="7bdde-180">これらのテーブルは Code First によって自動的に作成されるようになっていますが、移行に移行された移行を選択しました。</span><span class="sxs-lookup"><span data-stu-id="7bdde-180">Although we let Code First automatically create these tables for us, now that we have opted in to Migrations they have been converted into a Migration.</span></span> <span data-ttu-id="7bdde-181">Code First は、この移行が既に適用されているローカルデータベースにも記録されています。</span><span class="sxs-lookup"><span data-stu-id="7bdde-181">Code First has also recorded in our local database that this Migration has already been applied.</span></span> <span data-ttu-id="7bdde-182">ファイル名のタイムスタンプは、順序付けの目的で使用されます。</span><span class="sxs-lookup"><span data-stu-id="7bdde-182">The timestamp on the filename is used for ordering purposes.</span></span>

    <span data-ttu-id="7bdde-183">次に、モデルに変更を加え、ブログクラスに Url プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-183">Now let’s make a change to our model, add a Url property to the Blog class:</span></span>

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }

    public virtual List<Post> Posts { get; set; }
}
```

-   <span data-ttu-id="7bdde-184">パッケージマネージャーコンソールで、[**追加-移行] AddUrl**コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-184">Run the **Add-Migration AddUrl** command in Package Manager Console.</span></span>
    <span data-ttu-id="7bdde-185">[移行の追加] コマンドは、前回の移行以降に行われた変更を確認し、検出された変更を含む新しい移行をスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="7bdde-185">The Add-Migration command checks for changes since your last migration and scaffolds a new migration with any changes that are found.</span></span> <span data-ttu-id="7bdde-186">移行に名前を付けることができます。この例では、移行 ' AddUrl ' を呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="7bdde-186">We can give migrations a name; in this case we are calling the migration ‘AddUrl’.</span></span>
    <span data-ttu-id="7bdde-187">スキャフォールディングコードは、文字列データを保持できる Url 列を dbo に追加する必要があることを示しています。ブログテーブル。</span><span class="sxs-lookup"><span data-stu-id="7bdde-187">The scaffolded code is saying that we need to add a Url column, that can hold string data, to the dbo.Blogs table.</span></span> <span data-ttu-id="7bdde-188">必要に応じて、スキャフォールディングコードを編集することもできますが、この場合は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="7bdde-188">If needed, we could edit the scaffolded code but that’s not required in this case.</span></span>

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

-   <span data-ttu-id="7bdde-189">パッケージマネージャーコンソールで、**データベースの更新**コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-189">Run the **Update-Database** command in Package Manager Console.</span></span> <span data-ttu-id="7bdde-190">このコマンドを実行すると、保留中の移行がデータベースに適用されます。</span><span class="sxs-lookup"><span data-stu-id="7bdde-190">This command will apply any pending migrations to the database.</span></span> <span data-ttu-id="7bdde-191">InitialCreate 移行は既に適用されているので、移行によって新しい AddUrl 移行が適用されます。</span><span class="sxs-lookup"><span data-stu-id="7bdde-191">Our InitialCreate migration has already been applied so migrations will just apply our new AddUrl migration.</span></span>
    <span data-ttu-id="7bdde-192">ヒント: データベースに対して実行されている SQL を表示するには、 **-Verbose**スイッチを使用して、データベースに対して実行される SQL を確認します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-192">Tip: You can use the **–Verbose** switch when calling Update-Database to see the SQL that is being executed against the database.</span></span>

<span data-ttu-id="7bdde-193">これで、新しい Url 列がデータベースのブログテーブルに追加されました。</span><span class="sxs-lookup"><span data-stu-id="7bdde-193">The new Url column is now added to the Blogs table in the database:</span></span>

![Url を含むスキーマ](~/ef6/media/schemawithurl.png)

## <a name="6-data-annotations"></a><span data-ttu-id="7bdde-195">6. データ注釈</span><span class="sxs-lookup"><span data-stu-id="7bdde-195">6. Data Annotations</span></span>

<span data-ttu-id="7bdde-196">ここまでで、EF では既定の規則を使用してモデルを検出できるようになりましたが、クラスが規則に従わず、さらに構成を実行できるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7bdde-196">So far we’ve just let EF discover the model using its default conventions, but there are going to be times when our classes don’t follow the conventions and we need to be able to perform further configuration.</span></span> <span data-ttu-id="7bdde-197">これには2つのオプションがあります。このセクションのデータ注釈については、次のセクションの fluent API を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7bdde-197">There are two options for this; we’ll look at Data Annotations in this section and then the fluent API in the next section.</span></span>

-   <span data-ttu-id="7bdde-198">モデルにユーザークラスを追加してみましょう。</span><span class="sxs-lookup"><span data-stu-id="7bdde-198">Let’s add a User class to our model</span></span>

``` csharp
public class User
{
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   <span data-ttu-id="7bdde-199">また、派生コンテキストにセットを追加する必要もあります。</span><span class="sxs-lookup"><span data-stu-id="7bdde-199">We also need to add a set to our derived context</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }
}
```

-   <span data-ttu-id="7bdde-200">移行を追加しようとすると、"*EntityType ' User ' にキーが定義されていません" というエラーが表示されます。この EntityType のキーを定義します。 "*</span><span class="sxs-lookup"><span data-stu-id="7bdde-200">If we tried to add a migration we’d get an error saying “*EntityType ‘User’ has no key defined. Define the key for this EntityType.”*</span></span> <span data-ttu-id="7bdde-201">EF では、ユーザー名がユーザーの主キーであることを把握する方法がありません。</span><span class="sxs-lookup"><span data-stu-id="7bdde-201">because EF has no way of knowing that Username should be the primary key for User.</span></span>
-   <span data-ttu-id="7bdde-202">ここではデータ注釈を使用します。そのため、Program.cs の先頭に using ステートメントを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7bdde-202">We’re going to use Data Annotations now so we need to add a using statement at the top of Program.cs</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
```

-   <span data-ttu-id="7bdde-203">次に、ユーザー名プロパティに注釈を付けて、それが主キーであることを識別します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-203">Now annotate the Username property to identify that it is the primary key</span></span>

``` csharp
public class User
{
    [Key]
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   <span data-ttu-id="7bdde-204">スキャフォールディングを使用**して移行を実行し、** これらの変更をデータベースに適用します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-204">Use the **Add-Migration AddUser** command to scaffold a migration to apply these changes to the database</span></span>
-   <span data-ttu-id="7bdde-205">新しい移行をデータベースに適用するには、**データベースの更新**コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-205">Run the **Update-Database** command to apply the new migration to the database</span></span>

<span data-ttu-id="7bdde-206">これで、新しいテーブルがデータベースに追加されました。</span><span class="sxs-lookup"><span data-stu-id="7bdde-206">The new table is now added to the database:</span></span>

![ユーザーを使用したスキーマ](~/ef6/media/schemawithusers.png)

<span data-ttu-id="7bdde-208">EF でサポートされている注釈の完全な一覧は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="7bdde-208">The full list of annotations supported by EF is:</span></span>

-   [<span data-ttu-id="7bdde-209">KeyAttribute</span><span class="sxs-lookup"><span data-stu-id="7bdde-209">KeyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.keyattribute)
-   [<span data-ttu-id="7bdde-210">Stringlength 属性</span><span class="sxs-lookup"><span data-stu-id="7bdde-210">StringLengthAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute)
-   [<span data-ttu-id="7bdde-211">MaxLengthAttribute</span><span class="sxs-lookup"><span data-stu-id="7bdde-211">MaxLengthAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.maxlengthattribute)
-   [<span data-ttu-id="7bdde-212">ConcurrencyCheckAttribute</span><span class="sxs-lookup"><span data-stu-id="7bdde-212">ConcurrencyCheckAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.concurrencycheckattribute)
-   [<span data-ttu-id="7bdde-213">RequiredAttribute</span><span class="sxs-lookup"><span data-stu-id="7bdde-213">RequiredAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute)
-   [<span data-ttu-id="7bdde-214">タイムスタンプ属性</span><span class="sxs-lookup"><span data-stu-id="7bdde-214">TimestampAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute)
-   [<span data-ttu-id="7bdde-215">ComplexTypeAttribute</span><span class="sxs-lookup"><span data-stu-id="7bdde-215">ComplexTypeAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.complextypeattribute)
-   [<span data-ttu-id="7bdde-216">ColumnAttribute</span><span class="sxs-lookup"><span data-stu-id="7bdde-216">ColumnAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.columnattribute)
-   [<span data-ttu-id="7bdde-217">TableAttribute</span><span class="sxs-lookup"><span data-stu-id="7bdde-217">TableAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.tableattribute)
-   [<span data-ttu-id="7bdde-218">InversePropertyAttribute</span><span class="sxs-lookup"><span data-stu-id="7bdde-218">InversePropertyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.inversepropertyattribute)
-   [<span data-ttu-id="7bdde-219">ForeignKeyAttribute</span><span class="sxs-lookup"><span data-stu-id="7bdde-219">ForeignKeyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.foreignkeyattribute)
-   [<span data-ttu-id="7bdde-220">DatabaseGeneratedAttribute</span><span class="sxs-lookup"><span data-stu-id="7bdde-220">DatabaseGeneratedAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedattribute)
-   [<span data-ttu-id="7bdde-221">NotMappedAttribute</span><span class="sxs-lookup"><span data-stu-id="7bdde-221">NotMappedAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.notmappedattribute)

## <a name="7-fluent-api"></a><span data-ttu-id="7bdde-222">7. Fluent API</span><span class="sxs-lookup"><span data-stu-id="7bdde-222">7. Fluent API</span></span>

<span data-ttu-id="7bdde-223">前のセクションでは、データ注釈を使用して、規則によって検出された内容を補完またはオーバーライドする方法を説明しました。</span><span class="sxs-lookup"><span data-stu-id="7bdde-223">In the previous section we looked at using Data Annotations to supplement or override what was detected by convention.</span></span> <span data-ttu-id="7bdde-224">モデルを構成するもう1つの方法は、Code First fluent API を使用することです。</span><span class="sxs-lookup"><span data-stu-id="7bdde-224">The other way to configure the model is via the Code First fluent API.</span></span>

<span data-ttu-id="7bdde-225">ほとんどのモデル構成は、単純なデータ注釈を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="7bdde-225">Most model configuration can be done using simple data annotations.</span></span> <span data-ttu-id="7bdde-226">Fluent API は、データ注釈では実現できない高度な構成に加えて、データ注釈が実行できるすべてのものを網羅したモデル構成を指定するより高度な方法です。</span><span class="sxs-lookup"><span data-stu-id="7bdde-226">The fluent API is a more advanced way of specifying model configuration that covers everything that data annotations can do in addition to some more advanced configuration not possible with data annotations.</span></span> <span data-ttu-id="7bdde-227">データ注釈と fluent API を一緒に使用することができます。</span><span class="sxs-lookup"><span data-stu-id="7bdde-227">Data annotations and the fluent API can be used together.</span></span>

<span data-ttu-id="7bdde-228">Fluent API にアクセスするには、DbContext で OnModelCreating メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="7bdde-228">To access the fluent API you override the OnModelCreating method in DbContext.</span></span> <span data-ttu-id="7bdde-229">\_名を表示するために、ユーザー DisplayName が格納されている列の名前を変更したいとします。</span><span class="sxs-lookup"><span data-stu-id="7bdde-229">Let’s say we wanted to rename the column that User.DisplayName is stored in to display\_name.</span></span>

-   <span data-ttu-id="7bdde-230">次のコードを使用して、BloggingContext で OnModelCreating メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="7bdde-230">Override the OnModelCreating method on BloggingContext with the following code</span></span>

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

-   <span data-ttu-id="7bdde-231">スキャフォールディングを使用**して移行を実行し、** これらの変更をデータベースに適用します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-231">Use the **Add-Migration ChangeDisplayName** command to scaffold a migration to apply these changes to the database.</span></span>
-   <span data-ttu-id="7bdde-232">新しい移行をデータベースに適用するには、**データベースの更新**コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-232">Run the **Update-Database** command to apply the new migration to the database.</span></span>

<span data-ttu-id="7bdde-233">DisplayName 列の名前が変更され、\_名が表示されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="7bdde-233">The DisplayName column is now renamed to display\_name:</span></span>

![表示名が変更されたスキーマ](~/ef6/media/schemawithdisplaynamerenamed.png)

## <a name="summary"></a><span data-ttu-id="7bdde-235">要約</span><span class="sxs-lookup"><span data-stu-id="7bdde-235">Summary</span></span>

<span data-ttu-id="7bdde-236">このチュートリアルでは、新しいデータベースを使用した Code First 開発について説明しました。</span><span class="sxs-lookup"><span data-stu-id="7bdde-236">In this walkthrough we looked at Code First development using a new database.</span></span> <span data-ttu-id="7bdde-237">クラスを使用してモデルを定義した後、そのモデルを使用してデータベースを作成し、データを格納および取得します。</span><span class="sxs-lookup"><span data-stu-id="7bdde-237">We defined a model using classes then used that model to create a database and store and retrieve data.</span></span> <span data-ttu-id="7bdde-238">データベースを作成した後は、モデルの進化に従ってスキーマを変更するために Code First Migrations を使用しました。</span><span class="sxs-lookup"><span data-stu-id="7bdde-238">Once the database was created we used Code First Migrations to change the schema as our model evolved.</span></span> <span data-ttu-id="7bdde-239">また、データ注釈と Fluent API を使用してモデルを構成する方法についても説明しました。</span><span class="sxs-lookup"><span data-stu-id="7bdde-239">We also saw how to configure a model using Data Annotations and the Fluent API.</span></span>
