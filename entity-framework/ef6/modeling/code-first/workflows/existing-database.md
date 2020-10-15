---
title: 既存のデータベースへの Code First-EF6
description: Entity Framework 6 の既存のデータベースへの Code First
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/workflows/existing-database
ms.openlocfilehash: 2305236f68cfdc9f6e1582cca1163abf380b4075
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066422"
---
# <a name="code-first-to-an-existing-database"></a><span data-ttu-id="7f9dd-103">既存のデータベースへの Code First</span><span class="sxs-lookup"><span data-stu-id="7f9dd-103">Code First to an Existing Database</span></span>
<span data-ttu-id="7f9dd-104">このビデオとステップバイステップのチュートリアルでは、既存のデータベースを対象とした Code First 開発の概要について説明します。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-104">This video and step-by-step walkthrough provide an introduction to Code First development targeting an existing database.</span></span> <span data-ttu-id="7f9dd-105">Code First では、C または VB.Net クラスを使用してモデルを定義でき \# ます。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-105">Code First allows you to define your model using C\# or VB.Net classes.</span></span> <span data-ttu-id="7f9dd-106">必要に応じて、クラスとプロパティの属性、または fluent API を使用して、追加の構成を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-106">Optionally additional configuration can be performed using attributes on your classes and properties or by using a fluent API.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="7f9dd-107">ビデオを見る</span><span class="sxs-lookup"><span data-stu-id="7f9dd-107">Watch the video</span></span>
<span data-ttu-id="7f9dd-108">このビデオは、 [Channel 9 で利用できるようになりました](https://channel9.msdn.com/blogs/ef/code-first-to-existing-database-ef6-1-onwards-)。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-108">This video is [now available on Channel 9](https://channel9.msdn.com/blogs/ef/code-first-to-existing-database-ef6-1-onwards-).</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="7f9dd-109">前提条件</span><span class="sxs-lookup"><span data-stu-id="7f9dd-109">Pre-Requisites</span></span>

<span data-ttu-id="7f9dd-110">このチュートリアルを完了するには、 **Visual Studio 2012** または **Visual Studio 2013** がインストールされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-110">You will need to have **Visual Studio 2012** or **Visual Studio 2013** installed to complete this walkthrough.</span></span>

<span data-ttu-id="7f9dd-111">また、 **Visual Studio の Entity Framework Tools**バージョン**6.1** (またはそれ以降) がインストールされている必要もあります。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-111">You will also need version **6.1** (or later) of the **Entity Framework Tools for Visual Studio** installed.</span></span> <span data-ttu-id="7f9dd-112">最新バージョンの Entity Framework Tools をインストールする方法については、「 [Get Entity Framework](xref:ef6/fundamentals/install) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-112">See [Get Entity Framework](xref:ef6/fundamentals/install) for information on installing the latest version of the Entity Framework Tools.</span></span>

## <a name="1-create-an-existing-database"></a><span data-ttu-id="7f9dd-113">1. 既存のデータベースを作成する</span><span class="sxs-lookup"><span data-stu-id="7f9dd-113">1. Create an Existing Database</span></span>

<span data-ttu-id="7f9dd-114">通常、既存のデータベースを対象とする場合は、既に作成されますが、このチュートリアルでは、にアクセスするデータベースを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-114">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="7f9dd-115">では、データベースを生成してみましょう。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-115">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="7f9dd-116">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="7f9dd-116">Open Visual Studio</span></span>
-   <span data-ttu-id="7f9dd-117">**ビュー- &gt; サーバーエクスプローラー**</span><span class="sxs-lookup"><span data-stu-id="7f9dd-117">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="7f9dd-118">[**データ接続-接続の &gt; 追加**] を右クリックします。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-118">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="7f9dd-119">**サーバーエクスプローラー**からデータベースに接続していない場合は、データソースとして**Microsoft SQL Server**を選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-119">If you haven’t connected to a database from **Server Explorer** before you’ll need to select **Microsoft SQL Server** as the data source</span></span>

    ![データ ソースを選択する](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="7f9dd-121">LocalDB インスタンスに接続し、データベース名として「 **ブログ** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-121">Connect to your LocalDB instance, and enter **Blogging** as the database name</span></span>

    ![LocalDB 接続](~/ef6/media/localdbconnection.png)

-   <span data-ttu-id="7f9dd-123">[ **OK]** を選択すると、新しいデータベースを作成するかどうかを確認するメッセージが表示されます。 [**はい**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-123">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![データベースの作成ダイアログ](~/ef6/media/createdatabasedialog.png)

-   <span data-ttu-id="7f9dd-125">新しいデータベースがサーバーエクスプローラーに表示され、右クリックして [**新しいクエリ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-125">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="7f9dd-126">次の SQL を新しいクエリにコピーし、クエリを右クリックして、[**実行**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-126">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

``` SQL
CREATE TABLE [dbo].[Blogs] (
    [BlogId] INT IDENTITY (1, 1) NOT NULL,
    [Name] NVARCHAR (200) NULL,
    [Url]  NVARCHAR (200) NULL,
    CONSTRAINT [PK_dbo.Blogs] PRIMARY KEY CLUSTERED ([BlogId] ASC)
);

CREATE TABLE [dbo].[Posts] (
    [PostId] INT IDENTITY (1, 1) NOT NULL,
    [Title] NVARCHAR (200) NULL,
    [Content] NTEXT NULL,
    [BlogId] INT NOT NULL,
    CONSTRAINT [PK_dbo.Posts] PRIMARY KEY CLUSTERED ([PostId] ASC),
    CONSTRAINT [FK_dbo.Posts_dbo.Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [dbo].[Blogs] ([BlogId]) ON DELETE CASCADE
);

INSERT INTO [dbo].[Blogs] ([Name],[Url])
VALUES ('The Visual Studio Blog', 'http://blogs.msdn.com/visualstudio/')

INSERT INTO [dbo].[Blogs] ([Name],[Url])
VALUES ('.NET Framework Blog', 'http://blogs.msdn.com/dotnet/')
```

## <a name="2-create-the-application"></a><span data-ttu-id="7f9dd-127">2. アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="7f9dd-127">2. Create the Application</span></span>

<span data-ttu-id="7f9dd-128">単純にするために、Code First を使用してデータアクセスを行う基本的なコンソールアプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-128">To keep things simple we will build a basic console application that uses Code First to do the data access:</span></span>

-   <span data-ttu-id="7f9dd-129">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="7f9dd-129">Open Visual Studio</span></span>
-   <span data-ttu-id="7f9dd-130">**ファイル- &gt; 新規 &gt; プロジェクト...**</span><span class="sxs-lookup"><span data-stu-id="7f9dd-130">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="7f9dd-131">左側のメニューと**コンソールアプリケーション**から [ **Windows** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-131">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="7f9dd-132">名前として「 **Codefirstexistingを** 入力してください」と入力します。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-132">Enter **CodeFirstExistingDatabaseSample** as the name</span></span>
-   <span data-ttu-id="7f9dd-133">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-133">Select **OK**</span></span>

 

## <a name="3-reverse-engineer-model"></a><span data-ttu-id="7f9dd-134">3. モデルのリバースエンジニアリング</span><span class="sxs-lookup"><span data-stu-id="7f9dd-134">3. Reverse Engineer Model</span></span>

<span data-ttu-id="7f9dd-135">ここでは、Visual Studio の Entity Framework Tools を使用して、データベースにマップする初期コードを生成します。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-135">We will use the Entity Framework Tools for Visual Studio to help us generate some initial code to map to the database.</span></span> <span data-ttu-id="7f9dd-136">これらのツールはコードを生成するだけで、必要に応じて手動で入力することもできます。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-136">These tools are just generating code that you could also type by hand if you prefer.</span></span>

-   <span data-ttu-id="7f9dd-137">**プロジェクト- &gt; 新しい項目の追加...**</span><span class="sxs-lookup"><span data-stu-id="7f9dd-137">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="7f9dd-138">左側のメニューから [ **データ** ] を選択し、[ADO.NET] をクリックし **Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="7f9dd-138">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="7f9dd-139">名前として「 **Bのログインコンテキスト**」と入力し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-139">Enter **BloggingContext** as the name and click **OK**</span></span>
-   <span data-ttu-id="7f9dd-140">**Entity Data Model ウィザード**が起動します。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-140">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="7f9dd-141">**データベースから Code First**を選択し、[**次へ**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-141">Select **Code First from Database** and click **Next**</span></span>

    ![ウィザード1つの CFE](~/ef6/media/wizardonecfe.png)

-   <span data-ttu-id="7f9dd-143">最初のセクションで作成したデータベースへの接続を選択し、[**次へ**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-143">Select the connection to the database you created in the first section and click **Next**</span></span>

    ![ウィザード 2 CFE](~/ef6/media/wizardtwocfe.png)

-   <span data-ttu-id="7f9dd-145">[**テーブル**] の横にあるチェックボックスをクリックしてすべてのテーブルをインポートし、[**完了**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-145">Click the checkbox next to **Tables** to import all tables and click **Finish**</span></span>

    ![ウィザード 3 CFE](~/ef6/media/wizardthreecfe.png)

<span data-ttu-id="7f9dd-147">リバースエンジニアリングのプロセスが完了し、いくつかの項目がプロジェクトに追加されたら、追加された内容を見てみましょう。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-147">Once the reverse engineer process completes a number of items will have been added to the project, let's take a look at what's been added.</span></span>

### <a name="configuration-file"></a><span data-ttu-id="7f9dd-148">構成ファイル</span><span class="sxs-lookup"><span data-stu-id="7f9dd-148">Configuration file</span></span>

<span data-ttu-id="7f9dd-149">App.config ファイルがプロジェクトに追加されました。このファイルには、既存のデータベースへの接続文字列が含まれています。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-149">An App.config file has been added to the project, this file contains the connection string to the existing database.</span></span>

``` xml
<connectionStrings>
  <add  
    name="BloggingContext"  
    connectionString="data source=(localdb)\mssqllocaldb;initial catalog=Blogging;integrated security=True;MultipleActiveResultSets=True;App=EntityFramework"  
    providerName="System.Data.SqlClient" />
</connectionStrings>
```

<span data-ttu-id="7f9dd-150">*構成ファイルには他にもいくつかの設定があります。これらは既定の EF 設定であり、データベースを作成する場所を Code First に指示します。既存のデータベースにマップしているため、この設定はアプリケーションでは無視されます。*</span><span class="sxs-lookup"><span data-stu-id="7f9dd-150">*You’ll notice some other settings in the configuration file too, these are default EF settings that tell Code First where to create databases. Since we are mapping to an existing database these setting will be ignored in our application.*</span></span>

### <a name="derived-context"></a><span data-ttu-id="7f9dd-151">派生コンテキスト</span><span class="sxs-lookup"><span data-stu-id="7f9dd-151">Derived Context</span></span>

<span data-ttu-id="7f9dd-152">**B.log コンテキスト**クラスがプロジェクトに追加されました。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-152">A **BloggingContext** class has been added to the project.</span></span> <span data-ttu-id="7f9dd-153">コンテキストは、データベースとのセッションを表します。これにより、データのクエリと保存が可能になります。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-153">The context represents a session with the database, allowing us to query and save data.</span></span>
<span data-ttu-id="7f9dd-154">このコンテキストは、モデル内の型ごとに\*\*Dbset &lt; TEntity &gt; \*\*を公開します。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-154">The context exposes a **DbSet&lt;TEntity&gt;** for each type in our model.</span></span> <span data-ttu-id="7f9dd-155">また、既定のコンストラクターは、 **name =** 構文を使用して基本コンストラクターを呼び出すことにも注意してください。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-155">You’ll also notice that the default constructor calls a base constructor using the **name=** syntax.</span></span> <span data-ttu-id="7f9dd-156">これは、このコンテキストに使用する接続文字列を構成ファイルから読み込む必要があることを Code First に伝えます。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-156">This tells Code First that the connection string to use for this context should be loaded from the configuration file.</span></span>

``` csharp
public partial class BloggingContext : DbContext
    {
        public BloggingContext()
            : base("name=BloggingContext")
        {
        }

        public virtual DbSet<Blog> Blogs { get; set; }
        public virtual DbSet<Post> Posts { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
        }
    }
```

<span data-ttu-id="7f9dd-157">*構成ファイルで接続文字列を使用する場合は、常に **name =** 構文を使用する必要があります。これにより、接続文字列が存在しない場合は、規則に従って新しいデータベースを作成するのではなく、Entity Framework がスローされます。*</span><span class="sxs-lookup"><span data-stu-id="7f9dd-157">*You should always use the **name=** syntax when you are using a connection string in the config file. This ensures that if the connection string is not present then Entity Framework will throw rather than creating a new database by convention.*</span></span>

### <a name="model-classes"></a><span data-ttu-id="7f9dd-158">モデル クラス。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-158">Model classes</span></span>

<span data-ttu-id="7f9dd-159">最後に、 **ブログ** と **Post** クラスもプロジェクトに追加されています。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-159">Finally, a **Blog** and **Post** class have also been added to the project.</span></span> <span data-ttu-id="7f9dd-160">これらは、モデルを構成するドメインクラスです。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-160">These are the domain classes that make up our model.</span></span> <span data-ttu-id="7f9dd-161">クラスに適用されるデータ注釈が表示され、Code First の規則が既存のデータベースの構造と一致しない構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-161">You'll see Data Annotations applied to the classes to specify configuration where the Code First conventions would not align with the structure of the existing database.</span></span> <span data-ttu-id="7f9dd-162">たとえば、Blog.Name と最大の最大長が**200**であるため、 **stringlength**注釈が**Blog.Name**と**Blog. Url**に表示されます Code First (既定では、データベースプロバイダー- **nvarchar (max SQL Server)** でサポートされているの長さを使用します)。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-162">For example, you'll see the **StringLength** annotation on **Blog.Name** and **Blog.Url** since they have a maximum length of **200** in the database (the Code First default is to use the maximun length supported by the database provider - **nvarchar(max)** in SQL Server).</span></span>

``` csharp
public partial class Blog
{
    public Blog()
    {
        Posts = new HashSet<Post>();
    }

    public int BlogId { get; set; }

    [StringLength(200)]
    public string Name { get; set; }

    [StringLength(200)]
    public string Url { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}
```

## <a name="4-reading--writing-data"></a><span data-ttu-id="7f9dd-163">4. データの読み取り & 書き込み</span><span class="sxs-lookup"><span data-stu-id="7f9dd-163">4. Reading & Writing Data</span></span>

<span data-ttu-id="7f9dd-164">モデルを作成したので、これを使用していくつかのデータにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-164">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="7f9dd-165">次に示すように、 **Program.cs**に**Main**メソッドを実装します。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-165">Implement the **Main** method in **Program.cs** as shown below.</span></span> <span data-ttu-id="7f9dd-166">このコードは、コンテキストの新しいインスタンスを作成し、それを使用して新しい **ブログ**を挿入します。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-166">This code creates a new instance of our context and then uses it to insert a new **Blog**.</span></span> <span data-ttu-id="7f9dd-167">次に、LINQ クエリを使用して、データベースのすべての **ブログ** を **タイトル**別にアルファベット順に並べ替えて取得します。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-167">Then it uses a LINQ query to retrieve all **Blogs** from the database ordered alphabetically by **Title**.</span></span>

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

<span data-ttu-id="7f9dd-168">これで、アプリケーションを実行してテストできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-168">You can now run the application and test it.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
.NET Framework Blog
ADO.NET Blog
The Visual Studio Blog
Press any key to exit...
```
 
## <a name="what-if-my-database-changes"></a><span data-ttu-id="7f9dd-169">データベースが変更した場合はどうなりますか?</span><span class="sxs-lookup"><span data-stu-id="7f9dd-169">What if My Database Changes?</span></span>

<span data-ttu-id="7f9dd-170">データベースへの Code First ウィザードは、クラスの開始点セットを生成するように設計されています。このクラスを微調整して変更することができます。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-170">The Code First to Database wizard is designed to generate a starting point set of classes that you can then tweak and modify.</span></span> <span data-ttu-id="7f9dd-171">データベーススキーマが変更された場合は、手動でクラスを編集するか、別のリバースエンジニアリングを実行してクラスを上書きすることができます。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-171">If your database schema changes you can either manually edit the classes or perform another reverse engineer to overwrite the classes.</span></span>

## <a name="using-code-first-migrations-to-an-existing-database"></a><span data-ttu-id="7f9dd-172">既存のデータベースへの Code First Migrations の使用</span><span class="sxs-lookup"><span data-stu-id="7f9dd-172">Using Code First Migrations to an Existing Database</span></span>

<span data-ttu-id="7f9dd-173">既存のデータベースで Code First Migrations を使用する場合は、「 [既存のデータベースへの Code First Migrations](xref:ef6/modeling/code-first/migrations/existing-database)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-173">If you want to use Code First Migrations with an existing database, see [Code First Migrations to an existing database](xref:ef6/modeling/code-first/migrations/existing-database).</span></span>

## <a name="summary"></a><span data-ttu-id="7f9dd-174">まとめ</span><span class="sxs-lookup"><span data-stu-id="7f9dd-174">Summary</span></span>

<span data-ttu-id="7f9dd-175">このチュートリアルでは、既存のデータベースを使用した Code First 開発について説明しました。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-175">In this walkthrough we looked at Code First development using an existing database.</span></span> <span data-ttu-id="7f9dd-176">Entity Framework Tools for Visual Studio を使用して、データベースにマップされた一連のクラスをリバースエンジニアリングし、データの格納と取得に使用しました。</span><span class="sxs-lookup"><span data-stu-id="7f9dd-176">We used the Entity Framework Tools for Visual Studio to reverse engineer a set of classes that mapped to the database and could be used to store and retrieve data.</span></span>
