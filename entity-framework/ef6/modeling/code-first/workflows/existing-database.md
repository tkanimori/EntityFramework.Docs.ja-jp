---
title: 既存のデータベース - EF6 code First
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: a7e60b74-973d-4480-868f-500a3899932e
caps.latest.revision: 3
ms.openlocfilehash: 47581a7ae9ff534d26ce82365bcbe2b254247495
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2018
ms.locfileid: "39122291"
---
# <a name="code-first-to-an-existing-database"></a><span data-ttu-id="3cf23-102">既存のデータベースの code First</span><span class="sxs-lookup"><span data-stu-id="3cf23-102">Code First to an Existing Database</span></span>
<span data-ttu-id="3cf23-103">このビデオ、およびステップ バイ ステップ チュートリアルでは、既存のデータベースを対象とする Code First の開発の概要を提供します。</span><span class="sxs-lookup"><span data-stu-id="3cf23-103">This video and step-by-step walkthrough provide an introduction to Code First development targeting an existing database.</span></span> <span data-ttu-id="3cf23-104">コードを最初に使用すると、C を使用して、モデル定義\#または VB.Net クラス。</span><span class="sxs-lookup"><span data-stu-id="3cf23-104">Code First allows you to define your model using C\# or VB.Net classes.</span></span> <span data-ttu-id="3cf23-105">必要に応じて追加の構成は、クラスやプロパティ、または fluent API を使用して属性を使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="3cf23-105">Optionally additional configuration can be performed using attributes on your classes and properties or by using a fluent API.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="3cf23-106">ビデオを見る</span><span class="sxs-lookup"><span data-stu-id="3cf23-106">Watch the video</span></span>
<span data-ttu-id="3cf23-107">このビデオは[Channel 9 で利用可能](http://channel9.msdn.com/blogs/ef/code-first-to-existing-database-ef6-1-onwards-)します。</span><span class="sxs-lookup"><span data-stu-id="3cf23-107">This video is [now available on Channel 9](http://channel9.msdn.com/blogs/ef/code-first-to-existing-database-ef6-1-onwards-).</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="3cf23-108">前提条件</span><span class="sxs-lookup"><span data-stu-id="3cf23-108">Pre-Requisites</span></span>

<span data-ttu-id="3cf23-109">必要ですが**Visual Studio 2012**または**Visual Studio 2013**このチュートリアルを完了するをインストールします。</span><span class="sxs-lookup"><span data-stu-id="3cf23-109">You will need to have **Visual Studio 2012** or **Visual Studio 2013** installed to complete this walkthrough.</span></span>

<span data-ttu-id="3cf23-110">バージョンも必要になります**6.1** (またはそれ以降) の**Entity Framework Tools for Visual Studio**をインストールします。</span><span class="sxs-lookup"><span data-stu-id="3cf23-110">You will also need version **6.1** (or later) of the **Entity Framework Tools for Visual Studio** installed.</span></span> <span data-ttu-id="3cf23-111">参照してください[Entity Framework の取得](~/ef6/fundamentals/install.md)については、Entity Framework Tools の最新バージョンをインストールします。</span><span class="sxs-lookup"><span data-stu-id="3cf23-111">See [Get Entity Framework](~/ef6/fundamentals/install.md) for information on installing the latest version of the Entity Framework Tools.</span></span>

## <a name="1-create-an-existing-database"></a><span data-ttu-id="3cf23-112">1.既存のデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="3cf23-112">1. Create an Existing Database</span></span>

<span data-ttu-id="3cf23-113">通常それは既に作成されて、既存のデータベースを対象としているときに、このチュートリアルではアクセスするデータベースを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3cf23-113">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="3cf23-114">データベースを生成してみましょう。</span><span class="sxs-lookup"><span data-stu-id="3cf23-114">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="3cf23-115">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="3cf23-115">Open Visual Studio</span></span>
-   <span data-ttu-id="3cf23-116">**ビュー -&gt;サーバー エクスプ ローラー**</span><span class="sxs-lookup"><span data-stu-id="3cf23-116">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="3cf23-117">右クリックして**データ接続 -&gt;接続の追加.**</span><span class="sxs-lookup"><span data-stu-id="3cf23-117">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="3cf23-118">データベースに接続していない場合は**サーバー エクスプ ローラー**を選択する必要があります前に**Microsoft SQL Server**データ ソースとして</span><span class="sxs-lookup"><span data-stu-id="3cf23-118">If you haven’t connected to a database from **Server Explorer** before you’ll need to select **Microsoft SQL Server** as the data source</span></span>

    ![SelectDataSource](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="3cf23-120">LocalDB インスタンスに接続し、入力**ブログ**データベース名として</span><span class="sxs-lookup"><span data-stu-id="3cf23-120">Connect to your LocalDB instance, and enter **Blogging** as the database name</span></span>

    ![LocalDBConnection](~/ef6/media/localdbconnection.png)

-   <span data-ttu-id="3cf23-122">選択**OK**かどうかは、新しいデータベースを作成するように要求がある **[はい]**</span><span class="sxs-lookup"><span data-stu-id="3cf23-122">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![CreateDatabaseDialog](~/ef6/media/createdatabasedialog.png)

-   <span data-ttu-id="3cf23-124">新しいデータベースがサーバー エクスプ ローラーに表示されますを右クリックして選択**新しいクエリ**</span><span class="sxs-lookup"><span data-stu-id="3cf23-124">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="3cf23-125">新しいクエリに、次の SQL をコピーし、クエリを選択します右クリックし、 **Execute**</span><span class="sxs-lookup"><span data-stu-id="3cf23-125">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

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

## <a name="2-create-the-application"></a><span data-ttu-id="3cf23-126">2.アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="3cf23-126">2. Create the Application</span></span>

<span data-ttu-id="3cf23-127">簡単に、Code First を使用して、データ アクセスを実行する基本的なコンソール アプリケーションをビルドしていきます。</span><span class="sxs-lookup"><span data-stu-id="3cf23-127">To keep things simple we’re going to build a basic console application that uses Code First to perform data access:</span></span>

-   <span data-ttu-id="3cf23-128">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="3cf23-128">Open Visual Studio</span></span>
-   <span data-ttu-id="3cf23-129">**ファイル -&gt;新機能 -&gt;プロジェクト.**</span><span class="sxs-lookup"><span data-stu-id="3cf23-129">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="3cf23-130">選択**Windows**左側のメニューと**コンソール アプリケーション**</span><span class="sxs-lookup"><span data-stu-id="3cf23-130">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="3cf23-131">入力**CodeFirstExistingDatabaseSample**名として</span><span class="sxs-lookup"><span data-stu-id="3cf23-131">Enter **CodeFirstExistingDatabaseSample** as the name</span></span>
-   <span data-ttu-id="3cf23-132">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3cf23-132">Select **OK**</span></span>

 

## <a name="3-reverse-engineer-model"></a><span data-ttu-id="3cf23-133">3.リバース エンジニア リング モデル</span><span class="sxs-lookup"><span data-stu-id="3cf23-133">3. Reverse Engineer Model</span></span>

<span data-ttu-id="3cf23-134">データベースにマップする初期コードを生成するために Entity Framework Tools for Visual Studio の使用になります。</span><span class="sxs-lookup"><span data-stu-id="3cf23-134">We’re going to make use of the Entity Framework Tools for Visual Studio to help us generate some initial code to map to the database.</span></span> <span data-ttu-id="3cf23-135">これらのツールが入力することも手動で使用する場合のコードを生成するだけです。</span><span class="sxs-lookup"><span data-stu-id="3cf23-135">These tools are just generating code that you could also type by hand if you prefer.</span></span>

-   <span data-ttu-id="3cf23-136">**プロジェクト -&gt;新しい項目を追加しています.**</span><span class="sxs-lookup"><span data-stu-id="3cf23-136">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="3cf23-137">選択**データ**左側のメニューをクリックし**ADO.NET Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="3cf23-137">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="3cf23-138">入力**BloggingContext**名をクリックします**OK**</span><span class="sxs-lookup"><span data-stu-id="3cf23-138">Enter **BloggingContext** as the name and click **OK**</span></span>
-   <span data-ttu-id="3cf23-139">これにより、起動、 **Entity Data Model ウィザード**</span><span class="sxs-lookup"><span data-stu-id="3cf23-139">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="3cf23-140">選択**データベースから Code First**クリック **[次へ]**</span><span class="sxs-lookup"><span data-stu-id="3cf23-140">Select **Code First from Database** and click **Next**</span></span>

    ![WizardOneCFE](~/ef6/media/wizardonecfe.png)

-   <span data-ttu-id="3cf23-142">最初のセクションで作成したデータベースへの接続を選択し、クリックして **[次へ]**</span><span class="sxs-lookup"><span data-stu-id="3cf23-142">Select the connection to the database you created in the first section and click **Next**</span></span>

    ![WizardTwoCFE](~/ef6/media/wizardtwocfe.png)

-   <span data-ttu-id="3cf23-144">横のチェック ボックスをクリックして**テーブル**すべてのテーブルのインポート をクリックして**完了**</span><span class="sxs-lookup"><span data-stu-id="3cf23-144">Click the checkbox next to **Tables** to import all tables and click **Finish**</span></span>

    ![WizardThreeCFE](~/ef6/media/wizardthreecfe.png)

<span data-ttu-id="3cf23-146">リバース エンジニア リング プロセスが完了項目の数が追加されて、プロジェクトにみましょう見て何が追加されています。</span><span class="sxs-lookup"><span data-stu-id="3cf23-146">Once the reverse engineer process completes a number of items will have been added to the project, let's take a look at what's been added.</span></span>

### <a name="configuration-file"></a><span data-ttu-id="3cf23-147">構成ファイル</span><span class="sxs-lookup"><span data-stu-id="3cf23-147">Configuration file</span></span>

<span data-ttu-id="3cf23-148">App.config ファイルが追加されました、プロジェクトにこのファイルには、既存のデータベースへの接続文字列が含まれています。</span><span class="sxs-lookup"><span data-stu-id="3cf23-148">An App.config file has been added to the project, this file contains the connection string to the existing database.</span></span>

``` xml
<connectionStrings>
  <add  
    name="BloggingContext"  
    connectionString="data source=(localdb)\mssqllocaldb;initial catalog=Blogging;integrated security=True;MultipleActiveResultSets=True;App=EntityFramework"  
    providerName="System.Data.SqlClient" />
</connectionStrings>
```

<span data-ttu-id="3cf23-149">*わかります構成ファイル内の他の設定も、これらは Code First のデータベースを作成する場所を指示する既定の EF 設定です。アプリケーションでは、これらの設定は無視されます、既存のデータベースにマッピングしましたので。*</span><span class="sxs-lookup"><span data-stu-id="3cf23-149">*You’ll notice some other settings in the configuration file too, these are default EF settings that tell Code First where to create databases. Since we are mapping to an existing database these setting will be ignored in our application.*</span></span>

### <a name="derived-context"></a><span data-ttu-id="3cf23-150">派生コンテキスト</span><span class="sxs-lookup"><span data-stu-id="3cf23-150">Derived Context</span></span>

<span data-ttu-id="3cf23-151">A **BloggingContext**クラスがプロジェクトに追加されました。</span><span class="sxs-lookup"><span data-stu-id="3cf23-151">A **BloggingContext** class has been added to the project.</span></span> <span data-ttu-id="3cf23-152">コンテキストは、クエリを実行し、データを保存することができます、データベースとのセッションを表します。</span><span class="sxs-lookup"><span data-stu-id="3cf23-152">The context represents a session with the database, allowing us to query and save data.</span></span>
<span data-ttu-id="3cf23-153">コンテキストを公開、 **DbSet&lt;TEntity&gt;** モデルの種類ごとにします。</span><span class="sxs-lookup"><span data-stu-id="3cf23-153">The context exposes a **DbSet&lt;TEntity&gt;** for each type in our model.</span></span> <span data-ttu-id="3cf23-154">また、基底コンス トラクターを使用して、既定のコンス トラクターを呼び出す、**名 =** 構文。</span><span class="sxs-lookup"><span data-stu-id="3cf23-154">You’ll also notice that the default constructor calls a base constructor using the **name=** syntax.</span></span> <span data-ttu-id="3cf23-155">このコンテキストで使用する接続文字列を構成ファイルから読み込む必要がある Code First 指示します。</span><span class="sxs-lookup"><span data-stu-id="3cf23-155">This tells Code First that the connection string to use for this context should be loaded from the configuration file.</span></span>

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

<span data-ttu-id="3cf23-156">*常に使用する必要があります、**名 =** 構文、構成ファイルで接続文字列を使用している場合。これにより、接続文字列が存在しない場合、Entity Framework がスローされます規則により、新しいデータベースを作成するのではなく。*</span><span class="sxs-lookup"><span data-stu-id="3cf23-156">*You should always use the **name=** syntax when you are using a connection string in the config file. This ensures that if the connection string is not present then Entity Framework will throw rather than creating a new database by convention.*</span></span>

### <a name="model-classes"></a><span data-ttu-id="3cf23-157">モデル クラス</span><span class="sxs-lookup"><span data-stu-id="3cf23-157">Model classes</span></span>

<span data-ttu-id="3cf23-158">最後に、**ブログ**と**Post**クラスもプロジェクトに追加されました。</span><span class="sxs-lookup"><span data-stu-id="3cf23-158">Finally, a **Blog** and **Post** class have also been added to the project.</span></span> <span data-ttu-id="3cf23-159">これらは、私たちのモデルを構成するドメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="3cf23-159">These are the domain classes that make up our model.</span></span> <span data-ttu-id="3cf23-160">Code First の規則は、既存のデータベースの構造と合っていない場所の構成を指定する、クラスに適用されるデータ注釈が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3cf23-160">You'll see Data Annotations applied to the classes to specify configuration where the Code First conventions would not align with the structure of the existing database.</span></span> <span data-ttu-id="3cf23-161">たとえばに表示、 **StringLength**注釈**Blog.Name**と**Blog.Url**の最大長があるため**200**で、データベース (Code First の既定値がデータベース プロバイダー - でサポートされる最大長を使用するには**nvarchar (max)** SQL server)。</span><span class="sxs-lookup"><span data-stu-id="3cf23-161">For example, you'll see the **StringLength** annotation on **Blog.Name** and **Blog.Url** since they have a maximum length of **200** in the database (the Code First default is to use the maximun length supported by the database provider - **nvarchar(max)** in SQL Server).</span></span>

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

## <a name="4-reading--writing-data"></a><span data-ttu-id="3cf23-162">4.読み取りとデータの書き込み</span><span class="sxs-lookup"><span data-stu-id="3cf23-162">4. Reading & Writing Data</span></span>

<span data-ttu-id="3cf23-163">使用して一部のデータにアクセスするには、モデルができました。</span><span class="sxs-lookup"><span data-stu-id="3cf23-163">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="3cf23-164">実装、 **Main**メソッド**Program.cs**次のようです。</span><span class="sxs-lookup"><span data-stu-id="3cf23-164">Implement the **Main** method in **Program.cs** as shown below.</span></span> <span data-ttu-id="3cf23-165">このコードは、コンテキストの新しいインスタンスを作成し、新しい挿入を使用して**ブログ**します。</span><span class="sxs-lookup"><span data-stu-id="3cf23-165">This code creates a new instance of our context and then uses it to insert a new **Blog**.</span></span> <span data-ttu-id="3cf23-166">すべてを取得する LINQ クエリを使用して**ブログ**でアルファベット順では、データベースから**タイトル**します。</span><span class="sxs-lookup"><span data-stu-id="3cf23-166">Then it uses a LINQ query to retrieve all **Blogs** from the database ordered alphabetically by **Title**.</span></span>

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

<span data-ttu-id="3cf23-167">アプリケーションを実行し、テストを実行できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="3cf23-167">You can now run the application and test it out.</span></span>

```
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
.NET Framework Blog
ADO.NET Blog
The Visual Studio Blog
Press any key to exit...
```
 
## <a name="what-if-my-database-changes"></a><span data-ttu-id="3cf23-168">データベースが変わった場合でしょうか。</span><span class="sxs-lookup"><span data-stu-id="3cf23-168">What if My Database Changes?</span></span>

<span data-ttu-id="3cf23-169">Code First Database ウィザードには、クラスを調整し、変更することができますの開始地点のセットを生成しています。</span><span class="sxs-lookup"><span data-stu-id="3cf23-169">The Code First to Database wizard is designed to generate a starting point set of classes that you can then tweak and modify.</span></span> <span data-ttu-id="3cf23-170">データベース スキーマが変更された場合か、手動でクラスを編集したりクラスを上書きする別のリバース エンジニア リングを実行できます。</span><span class="sxs-lookup"><span data-stu-id="3cf23-170">If your database schema changes you can either manually edit the classes or perform another reverse engineer to overwrite the classes.</span></span>

## <a name="using-code-first-migrations-to-an-existing-database"></a><span data-ttu-id="3cf23-171">既存のデータベースの Code First Migrations を使用します。</span><span class="sxs-lookup"><span data-stu-id="3cf23-171">Using Code First Migrations to an Existing Database</span></span>

<span data-ttu-id="3cf23-172">既存のデータベースで Code First Migrations を使用する場合を参照してください。[既存のデータベースの Code First Migrations](~/ef6/modeling/code-first/migrations/existing-database.md)します。</span><span class="sxs-lookup"><span data-stu-id="3cf23-172">If you want to use Code First Migrations with an existing database, see [Code First Migrations to an existing database](~/ef6/modeling/code-first/migrations/existing-database.md).</span></span>

## <a name="summary"></a><span data-ttu-id="3cf23-173">まとめ</span><span class="sxs-lookup"><span data-stu-id="3cf23-173">Summary</span></span>

<span data-ttu-id="3cf23-174">このチュートリアルでは、既存のデータベースを使用して Code First の開発に説明しました。</span><span class="sxs-lookup"><span data-stu-id="3cf23-174">In this walkthrough we looked at Code First development using an existing database.</span></span> <span data-ttu-id="3cf23-175">Entity Framework Tools for Visual Studio を使用して、リバース エンジニア リング、データベースにマップされ、データを格納および取得される可能性がありますクラスのセットをしました。</span><span class="sxs-lookup"><span data-stu-id="3cf23-175">We used the Entity Framework Tools for Visual Studio to reverse engineer a set of classes that mapped to the database and could be used to store and retrieve data.</span></span>
