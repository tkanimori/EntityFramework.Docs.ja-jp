---
title: Database First-EF6
description: Entity Framework 6 の Database First
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/workflows/database-first
ms.openlocfilehash: 4046144ff3f1ecb4a3c9e7d4cb5d6c462c3782c7
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066110"
---
# <a name="database-first"></a><span data-ttu-id="c038e-103">Database First</span><span class="sxs-lookup"><span data-stu-id="c038e-103">Database First</span></span>
<span data-ttu-id="c038e-104">このビデオとステップバイステップのチュートリアルでは、Entity Framework を使用した Database First 開発の概要について説明します。</span><span class="sxs-lookup"><span data-stu-id="c038e-104">This video and step-by-step walkthrough provide an introduction to Database First development using Entity Framework.</span></span> <span data-ttu-id="c038e-105">Database First を使用すると、既存のデータベースからモデルをリバースエンジニアリングできます。</span><span class="sxs-lookup"><span data-stu-id="c038e-105">Database First allows you to reverse engineer a model from an existing database.</span></span> <span data-ttu-id="c038e-106">モデルは EDMX ファイル (.edmx 拡張子) に格納されており、Entity Framework Designer で表示および編集できます。</span><span class="sxs-lookup"><span data-stu-id="c038e-106">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="c038e-107">アプリケーションで対話するクラスは、EDMX ファイルから自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="c038e-107">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="c038e-108">ビデオを見る</span><span class="sxs-lookup"><span data-stu-id="c038e-108">Watch the video</span></span>
<span data-ttu-id="c038e-109">このビデオでは、Entity Framework を使用した Database First 開発の概要について説明します。</span><span class="sxs-lookup"><span data-stu-id="c038e-109">This video provides an introduction to Database First development using Entity Framework.</span></span> <span data-ttu-id="c038e-110">Database First を使用すると、既存のデータベースからモデルをリバースエンジニアリングできます。</span><span class="sxs-lookup"><span data-stu-id="c038e-110">Database First allows you to reverse engineer a model from an existing database.</span></span> <span data-ttu-id="c038e-111">モデルは EDMX ファイル (.edmx 拡張子) に格納されており、Entity Framework Designer で表示および編集できます。</span><span class="sxs-lookup"><span data-stu-id="c038e-111">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="c038e-112">アプリケーションで対話するクラスは、EDMX ファイルから自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="c038e-112">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

<span data-ttu-id="c038e-113">**提供**: [Rowan Miller](https://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="c038e-113">**Presented By**: [Rowan Miller](https://romiller.com/)</span></span>

<span data-ttu-id="c038e-114">**ビデオ**: [WMV](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv)  |  [MP4](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v)  |  [WMV (ZIP)](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)</span><span class="sxs-lookup"><span data-stu-id="c038e-114">**Video**: [WMV](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv) | [MP4](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="c038e-115">前提条件</span><span class="sxs-lookup"><span data-stu-id="c038e-115">Pre-Requisites</span></span>

<span data-ttu-id="c038e-116">このチュートリアルを完了するには、少なくとも Visual Studio 2010 または Visual Studio 2012 がインストールされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="c038e-116">You will need to have at least Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="c038e-117">Visual Studio 2010 を使用している場合は、 [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) もインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="c038e-117">If you are using Visual Studio 2010, you will also need to have [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

 

## <a name="1-create-an-existing-database"></a><span data-ttu-id="c038e-118">1. 既存のデータベースを作成する</span><span class="sxs-lookup"><span data-stu-id="c038e-118">1. Create an Existing Database</span></span>

<span data-ttu-id="c038e-119">通常、既存のデータベースを対象とする場合は、既に作成されますが、このチュートリアルでは、にアクセスするデータベースを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c038e-119">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="c038e-120">Visual Studio と共にインストールされるデータベースサーバーは、インストールされている Visual Studio のバージョンによって異なります。</span><span class="sxs-lookup"><span data-stu-id="c038e-120">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="c038e-121">Visual Studio 2010 を使用している場合は、SQL Express データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="c038e-121">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="c038e-122">Visual Studio 2012 を使用している場合は、 [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="c038e-122">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) database.</span></span>

 

<span data-ttu-id="c038e-123">では、データベースを生成してみましょう。</span><span class="sxs-lookup"><span data-stu-id="c038e-123">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="c038e-124">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="c038e-124">Open Visual Studio</span></span>
-   <span data-ttu-id="c038e-125">**ビュー- &gt; サーバーエクスプローラー**</span><span class="sxs-lookup"><span data-stu-id="c038e-125">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="c038e-126">[**データ接続-接続の &gt; 追加**] を右クリックします。</span><span class="sxs-lookup"><span data-stu-id="c038e-126">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="c038e-127">サーバーエクスプローラーからデータベースに接続していない場合は、データソースとして Microsoft SQL Server を選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c038e-127">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![データ ソースを選択する](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="c038e-129">インストールされているものに応じて LocalDB または SQL Express に接続し、データベース名として「Databasefirst」と入力し**ます。**</span><span class="sxs-lookup"><span data-stu-id="c038e-129">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **DatabaseFirst.Blogging** as the database name</span></span>

    ![Sql Express 接続 DF](~/ef6/media/sqlexpressconnectiondf.png)

    ![LocalDB 接続 DF](~/ef6/media/localdbconnectiondf.png)

-   <span data-ttu-id="c038e-132">[ **OK]** を選択すると、新しいデータベースを作成するかどうかを確認するメッセージが表示されます。 [**はい**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c038e-132">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![データベースの作成ダイアログ](~/ef6/media/createdatabasedialog.png)

-   <span data-ttu-id="c038e-134">新しいデータベースがサーバーエクスプローラーに表示され、右クリックして [**新しいクエリ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c038e-134">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="c038e-135">次の SQL を新しいクエリにコピーし、クエリを右クリックして、[**実行**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c038e-135">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

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
```

## <a name="2-create-the-application"></a><span data-ttu-id="c038e-136">2. アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="c038e-136">2. Create the Application</span></span>

<span data-ttu-id="c038e-137">単純にするために、Database First を使用してデータアクセスを実行する基本的なコンソールアプリケーションを構築します。</span><span class="sxs-lookup"><span data-stu-id="c038e-137">To keep things simple we’re going to build a basic console application that uses the Database First to perform data access:</span></span>

-   <span data-ttu-id="c038e-138">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="c038e-138">Open Visual Studio</span></span>
-   <span data-ttu-id="c038e-139">**ファイル- &gt; 新規 &gt; プロジェクト...**</span><span class="sxs-lookup"><span data-stu-id="c038e-139">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="c038e-140">左側のメニューと**コンソールアプリケーション**から [ **Windows** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c038e-140">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="c038e-141">名前として「 **Databasefirstsample** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="c038e-141">Enter **DatabaseFirstSample** as the name</span></span>
-   <span data-ttu-id="c038e-142">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c038e-142">Select **OK**</span></span>

 

## <a name="3-reverse-engineer-model"></a><span data-ttu-id="c038e-143">3. モデルのリバースエンジニアリング</span><span class="sxs-lookup"><span data-stu-id="c038e-143">3. Reverse Engineer Model</span></span>

<span data-ttu-id="c038e-144">ここでは、Visual Studio の一部として含まれている Entity Framework Designer を使用して、モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="c038e-144">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="c038e-145">**プロジェクト- &gt; 新しい項目の追加...**</span><span class="sxs-lookup"><span data-stu-id="c038e-145">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="c038e-146">左側のメニューから [ **データ** ] を選択し、[ADO.NET] をクリックし **Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="c038e-146">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="c038e-147">名前として「 **Bのログインモデル**」と入力し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="c038e-147">Enter **BloggingModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="c038e-148">**Entity Data Model ウィザード**が起動します。</span><span class="sxs-lookup"><span data-stu-id="c038e-148">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="c038e-149">[**データベースから生成**] を選択し、[**次へ**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="c038e-149">Select **Generate from Database** and click **Next**</span></span>

    ![ウィザードの手順 1.](~/ef6/media/wizardstep1.png)

-   <span data-ttu-id="c038e-151">最初のセクションで作成したデータベースへの接続を選択し、接続文字列の名前として「 **Bのログインコンテキスト**」と入力して、[**次へ**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="c038e-151">Select the connection to the database you created in the first section, enter **BloggingContext** as the name of the connection string and click **Next**</span></span>

    ![ウィザードの手順 2.](~/ef6/media/wizardstep2.png)

-   <span data-ttu-id="c038e-153">[テーブル] の横にあるチェックボックスをオンにしてすべてのテーブルをインポートし、[完了] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="c038e-153">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![ウィザードの手順 3.](~/ef6/media/wizardstep3.png)

 

<span data-ttu-id="c038e-155">リバースエンジニアリングプロセスが完了すると、新しいモデルがプロジェクトに追加され、Entity Framework Designer で表示できるように開かれます。</span><span class="sxs-lookup"><span data-stu-id="c038e-155">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="c038e-156">データベースの接続の詳細を含む App.config ファイルもプロジェクトに追加されています。</span><span class="sxs-lookup"><span data-stu-id="c038e-156">An App.config file has also been added to your project with the connection details for the database.</span></span>

![モデルの初期](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="c038e-158">Visual Studio 2010 の追加手順</span><span class="sxs-lookup"><span data-stu-id="c038e-158">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="c038e-159">Visual Studio 2010 で作業している場合、Entity Framework の最新バージョンにアップグレードするには、いくつかの追加の手順に従う必要があります。</span><span class="sxs-lookup"><span data-stu-id="c038e-159">If you are working in Visual Studio 2010 there are some additional steps you need to follow to upgrade to the latest version of Entity Framework.</span></span> <span data-ttu-id="c038e-160">アップグレードは、強化された API サーフェイスにアクセスできるようにするために重要です。これは、より使いやすく、最新のバグ修正にも使用できます。</span><span class="sxs-lookup"><span data-stu-id="c038e-160">Upgrading is important because it gives you access to an improved API surface, that is much easier to use, as well as the latest bug fixes.</span></span>

<span data-ttu-id="c038e-161">まず、NuGet から最新バージョンの Entity Framework を取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c038e-161">First up, we need to get the latest version of Entity Framework from NuGet.</span></span>

-   <span data-ttu-id="c038e-162">**プロジェクト– &gt;NuGet パッケージの管理...** 
     *[ **Nuget パッケージの管理...** ] オプションがない場合は、[最新バージョンの nuget](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)をインストールする必要があり*ます。</span><span class="sxs-lookup"><span data-stu-id="c038e-162">**Project –&gt; Manage NuGet Packages…**
*If you don’t have the **Manage NuGet Packages…** option you should install the [latest version of NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)*</span></span>
-   <span data-ttu-id="c038e-163">[ **オンライン** ] タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="c038e-163">Select the **Online** tab</span></span>
-   <span data-ttu-id="c038e-164">**Entityframework**パッケージを選択します</span><span class="sxs-lookup"><span data-stu-id="c038e-164">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="c038e-165">**[Install]** (インストール) をクリックします。</span><span class="sxs-lookup"><span data-stu-id="c038e-165">Click **Install**</span></span>

<span data-ttu-id="c038e-166">次に、モデルをスワップして、新しいバージョンの Entity Framework で導入された DbContext API を使用するコードを生成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c038e-166">Next, we need to swap our model to generate code that makes use of the DbContext API, which was introduced in later versions of Entity Framework.</span></span>

-   <span data-ttu-id="c038e-167">EF デザイナーでモデルの空の場所を右クリックし、[**コード生成項目の追加...** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c038e-167">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="c038e-168">左側のメニューから [**オンラインテンプレート**] を選択し、 **dbcontext**を検索します。</span><span class="sxs-lookup"><span data-stu-id="c038e-168">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="c038e-169">**C \# の EF 5.X Dbcontext ジェネレーター**を選択し、名前として「 **bの出力モデル**」と入力して、[**追加**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="c038e-169">Select the EF **5.x DbContext Generator for C\#**, enter **BloggingModel** as the name and click **Add**</span></span>

    ![DbContext テンプレート](~/ef6/media/dbcontexttemplate.png)

 

## <a name="4-reading--writing-data"></a><span data-ttu-id="c038e-171">4. データの読み取り & 書き込み</span><span class="sxs-lookup"><span data-stu-id="c038e-171">4. Reading & Writing Data</span></span>

<span data-ttu-id="c038e-172">モデルを作成したので、これを使用していくつかのデータにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="c038e-172">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="c038e-173">データへのアクセスに使用するクラスは、EDMX ファイルに基づいて自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="c038e-173">The classes we are going to use to access data are being automatically generated for you based on the EDMX file.</span></span>

<span data-ttu-id="c038e-174">*このスクリーンショットは Visual Studio 2012 からのものです。 Visual Studio 2010 を使用している場合は、BloggingModel.tt ファイルと BloggingModel.Context.tt ファイルは、EDMX ファイルの下に入れ子になっているのではなく、プロジェクトの直下に配置されます。*</span><span class="sxs-lookup"><span data-stu-id="c038e-174">*This screen shot is from Visual Studio 2012, if you are using Visual Studio 2010 the BloggingModel.tt and BloggingModel.Context.tt files will be directly under your project rather than nested under the EDMX file.*</span></span>

![生成されたクラス DF](~/ef6/media/generatedclassesdf.png)

 

<span data-ttu-id="c038e-176">次に示すように、Program.cs に Main メソッドを実装します。</span><span class="sxs-lookup"><span data-stu-id="c038e-176">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="c038e-177">このコードは、コンテキストの新しいインスタンスを作成し、それを使用して新しいブログを挿入します。</span><span class="sxs-lookup"><span data-stu-id="c038e-177">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="c038e-178">次に、LINQ クエリを使用して、データベースのすべてのブログをタイトル別にアルファベット順に並べ替えて取得します。</span><span class="sxs-lookup"><span data-stu-id="c038e-178">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

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

<span data-ttu-id="c038e-179">これで、アプリケーションを実行してテストできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="c038e-179">You can now run the application and test it out.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
 

## <a name="5-dealing-with-database-changes"></a><span data-ttu-id="c038e-180">5. データベースの変更に対処する</span><span class="sxs-lookup"><span data-stu-id="c038e-180">5. Dealing with Database Changes</span></span>

<span data-ttu-id="c038e-181">次に、データベーススキーマをいくつか変更します。これらの変更を行うときは、これらの変更を反映するようにモデルを更新する必要もあります。</span><span class="sxs-lookup"><span data-stu-id="c038e-181">Now it’s time to make some changes to our database schema, when we make these changes we also need to update our model to reflect those changes.</span></span>

<span data-ttu-id="c038e-182">最初の手順では、データベーススキーマにいくつかの変更を行います。</span><span class="sxs-lookup"><span data-stu-id="c038e-182">The first step is to make some changes to the database schema.</span></span> <span data-ttu-id="c038e-183">ここでは、ユーザーテーブルをスキーマに追加します。</span><span class="sxs-lookup"><span data-stu-id="c038e-183">We’re going to add a Users table to the schema.</span></span>

-   <span data-ttu-id="c038e-184">サーバーエクスプローラーで**Databasefirst. ブログ**データベースを右クリックし、[**新しいクエリ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c038e-184">Right-click on the **DatabaseFirst.Blogging** database in Server Explorer and select **New Query**</span></span>
-   <span data-ttu-id="c038e-185">次の SQL を新しいクエリにコピーし、クエリを右クリックして、[**実行**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c038e-185">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

``` SQL
CREATE TABLE [dbo].[Users]
(
    [Username] NVARCHAR(50) NOT NULL PRIMARY KEY,  
    [DisplayName] NVARCHAR(MAX) NULL
)
```

<span data-ttu-id="c038e-186">スキーマが更新されたので、これらの変更によってモデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="c038e-186">Now that the schema is updated, it’s time to update the model with those changes.</span></span>

-   <span data-ttu-id="c038e-187">EF デザイナーでモデルの空の場所を右クリックし、[データベースからモデルを更新...] を選択すると、更新ウィザードが起動します。</span><span class="sxs-lookup"><span data-stu-id="c038e-187">Right-click on an empty spot of your model in the EF Designer and select ‘Update Model from Database…’, this will launch the Update Wizard</span></span>
-   <span data-ttu-id="c038e-188">更新ウィザードの [追加] タブで、[テーブル] の横にあるチェックボックスをオンにします。これは、スキーマから新しいテーブルを追加することを示します。</span><span class="sxs-lookup"><span data-stu-id="c038e-188">On the Add tab of the Update Wizard check the box next to Tables, this indicates that we want to add any new tables from the schema.</span></span>
    <span data-ttu-id="c038e-189">*[更新] タブには、更新中に変更がチェックされる、モデル内の既存のテーブルが表示されます。[削除] タブには、スキーマから削除されたテーブルが表示され、更新の一部としてモデルからも削除されます。これら2つのタブの情報は自動的に検出され、情報提供のみを目的として提供されます。設定を変更することはできません。*</span><span class="sxs-lookup"><span data-stu-id="c038e-189">*The Refresh tab shows any existing tables in the model that will be checked for changes during the update. The Delete tabs show any tables that have been removed from the schema and will also be removed from the model as part of the update. The information on these two tabs is automatically detected and is provided for informational purposes only, you cannot change any settings.*</span></span>

    ![更新ウィザード](~/ef6/media/refreshwizard.png)

-   <span data-ttu-id="c038e-191">更新ウィザードで [完了] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="c038e-191">Click Finish on the Update Wizard</span></span>

 

<span data-ttu-id="c038e-192">これで、モデルが更新され、データベースに追加したユーザーテーブルにマップされる新しいユーザーエンティティが追加されました。</span><span class="sxs-lookup"><span data-stu-id="c038e-192">The model is now updated to include a new User entity that maps to the Users table we added to the database.</span></span>

![更新されたモデル](~/ef6/media/modelupdated.png)

## <a name="summary"></a><span data-ttu-id="c038e-194">まとめ</span><span class="sxs-lookup"><span data-stu-id="c038e-194">Summary</span></span>

<span data-ttu-id="c038e-195">このチュートリアルでは Database First 開発について説明しました。これにより、既存のデータベースに基づいて EF デザイナーでモデルを作成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="c038e-195">In this walkthrough we looked at Database First development, which allowed us to create a model in the EF Designer based on an existing database.</span></span> <span data-ttu-id="c038e-196">次に、そのモデルを使用して、データベースのデータの読み取りと書き込みを行います。</span><span class="sxs-lookup"><span data-stu-id="c038e-196">We then used that model to read and write some data from the database.</span></span> <span data-ttu-id="c038e-197">最後に、データベーススキーマに加えられた変更を反映するようにモデルを更新しました。</span><span class="sxs-lookup"><span data-stu-id="c038e-197">Finally, we updated the model to reflect changes we made to the database schema.</span></span>
