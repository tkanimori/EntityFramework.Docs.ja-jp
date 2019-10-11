---
title: Database First-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: cc6ffdb3-388d-4e79-a201-01ec2577c949
ms.openlocfilehash: d40cff4ddccf43a394ef4f244653372a5a89b05a
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182449"
---
# <a name="database-first"></a><span data-ttu-id="7a93a-102">Database First</span><span class="sxs-lookup"><span data-stu-id="7a93a-102">Database First</span></span>
<span data-ttu-id="7a93a-103">このビデオとステップバイステップのチュートリアルでは、Entity Framework を使用した Database First 開発の概要について説明します。</span><span class="sxs-lookup"><span data-stu-id="7a93a-103">This video and step-by-step walkthrough provide an introduction to Database First development using Entity Framework.</span></span> <span data-ttu-id="7a93a-104">Database First を使用すると、既存のデータベースからモデルをリバースエンジニアリングできます。</span><span class="sxs-lookup"><span data-stu-id="7a93a-104">Database First allows you to reverse engineer a model from an existing database.</span></span> <span data-ttu-id="7a93a-105">モデルは EDMX ファイル (.edmx 拡張子) に格納されており、Entity Framework Designer で表示および編集できます。</span><span class="sxs-lookup"><span data-stu-id="7a93a-105">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="7a93a-106">アプリケーションで対話するクラスは、EDMX ファイルから自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="7a93a-106">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="7a93a-107">ビデオを見る</span><span class="sxs-lookup"><span data-stu-id="7a93a-107">Watch the video</span></span>
<span data-ttu-id="7a93a-108">このビデオでは、Entity Framework を使用した Database First 開発の概要について説明します。</span><span class="sxs-lookup"><span data-stu-id="7a93a-108">This video provides an introduction to Database First development using Entity Framework.</span></span> <span data-ttu-id="7a93a-109">Database First を使用すると、既存のデータベースからモデルをリバースエンジニアリングできます。</span><span class="sxs-lookup"><span data-stu-id="7a93a-109">Database First allows you to reverse engineer a model from an existing database.</span></span> <span data-ttu-id="7a93a-110">モデルは EDMX ファイル (.edmx 拡張子) に格納されており、Entity Framework Designer で表示および編集できます。</span><span class="sxs-lookup"><span data-stu-id="7a93a-110">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="7a93a-111">アプリケーションで対話するクラスは、EDMX ファイルから自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="7a93a-111">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

<span data-ttu-id="7a93a-112">**表示者**:[Rowan 明美](https://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="7a93a-112">**Presented By**: [Rowan Miller](https://romiller.com/)</span></span>

<span data-ttu-id="7a93a-113">**ビデオ**:[WMV](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv) | [MP4](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)</span><span class="sxs-lookup"><span data-stu-id="7a93a-113">**Video**: [WMV](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv) | [MP4](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="7a93a-114">前提条件</span><span class="sxs-lookup"><span data-stu-id="7a93a-114">Pre-Requisites</span></span>

<span data-ttu-id="7a93a-115">このチュートリアルを完了するには、少なくとも Visual Studio 2010 または Visual Studio 2012 がインストールされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a93a-115">You will need to have at least Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="7a93a-116">Visual Studio 2010 を使用している場合は、 [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)もインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a93a-116">If you are using Visual Studio 2010, you will also need to have [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

 

## <a name="1-create-an-existing-database"></a><span data-ttu-id="7a93a-117">1. 既存のデータベースを作成する</span><span class="sxs-lookup"><span data-stu-id="7a93a-117">1. Create an Existing Database</span></span>

<span data-ttu-id="7a93a-118">通常、既存のデータベースを対象とする場合は、既に作成されますが、このチュートリアルでは、にアクセスするデータベースを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a93a-118">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="7a93a-119">Visual Studio と共にインストールされるデータベースサーバーは、インストールされている Visual Studio のバージョンによって異なります。</span><span class="sxs-lookup"><span data-stu-id="7a93a-119">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="7a93a-120">Visual Studio 2010 を使用している場合は、SQL Express データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="7a93a-120">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="7a93a-121">Visual Studio 2012 を使用している場合は、 [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx)データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="7a93a-121">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) database.</span></span>

 

<span data-ttu-id="7a93a-122">では、データベースを生成してみましょう。</span><span class="sxs-lookup"><span data-stu-id="7a93a-122">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="7a93a-123">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="7a93a-123">Open Visual Studio</span></span>
-   <span data-ttu-id="7a93a-124">**ビュー-&gt; サーバーエクスプローラー**</span><span class="sxs-lookup"><span data-stu-id="7a93a-124">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="7a93a-125">[データ接続] を右クリックし **、&gt; [接続の追加**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7a93a-125">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="7a93a-126">サーバーエクスプローラーからデータベースに接続していない場合は、データソースとして Microsoft SQL Server を選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a93a-126">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![データソースの選択](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="7a93a-128">インストールされているものに応じて LocalDB または SQL Express に接続し、データベース名として「Databasefirst」と入力し**ます。**</span><span class="sxs-lookup"><span data-stu-id="7a93a-128">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **DatabaseFirst.Blogging** as the database name</span></span>

    ![Sql Express 接続 DF](~/ef6/media/sqlexpressconnectiondf.png)

    ![LocalDB 接続 DF](~/ef6/media/localdbconnectiondf.png)

-   <span data-ttu-id="7a93a-131">[ **OK]** を選択すると、新しいデータベースを作成するかどうかを確認するメッセージが表示されます。 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7a93a-131">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![データベースの作成ダイアログ](~/ef6/media/createdatabasedialog.png)

-   <span data-ttu-id="7a93a-133">新しいデータベースがサーバーエクスプローラーに表示され、右クリックして **[新しいクエリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7a93a-133">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="7a93a-134">次の SQL を新しいクエリにコピーし、クエリを右クリックして、 **[実行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7a93a-134">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

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

## <a name="2-create-the-application"></a><span data-ttu-id="7a93a-135">2. アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="7a93a-135">2. Create the Application</span></span>

<span data-ttu-id="7a93a-136">単純にするために、Database First を使用してデータアクセスを実行する基本的なコンソールアプリケーションを構築します。</span><span class="sxs-lookup"><span data-stu-id="7a93a-136">To keep things simple we’re going to build a basic console application that uses the Database First to perform data access:</span></span>

-   <span data-ttu-id="7a93a-137">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="7a93a-137">Open Visual Studio</span></span>
-   <span data-ttu-id="7a93a-138">**ファイル-&gt; 新規-@no__t プロジェクト...**</span><span class="sxs-lookup"><span data-stu-id="7a93a-138">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="7a93a-139">左側のメニューと**コンソールアプリケーション**から **[Windows]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7a93a-139">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="7a93a-140">名前として「 **Databasefirstsample** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="7a93a-140">Enter **DatabaseFirstSample** as the name</span></span>
-   <span data-ttu-id="7a93a-141">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7a93a-141">Select **OK**</span></span>

 

## <a name="3-reverse-engineer-model"></a><span data-ttu-id="7a93a-142">3.リバースエンジニアリングモデル</span><span class="sxs-lookup"><span data-stu-id="7a93a-142">3. Reverse Engineer Model</span></span>

<span data-ttu-id="7a93a-143">ここでは、Visual Studio の一部として含まれている Entity Framework Designer を使用して、モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="7a93a-143">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="7a93a-144">**プロジェクト-&gt; 新しい項目の追加...**</span><span class="sxs-lookup"><span data-stu-id="7a93a-144">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="7a93a-145">左側のメニューから **データ** を選択し、ADO.NET をクリックし**Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="7a93a-145">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="7a93a-146">名前として「 **Bのログインモデル**」と入力し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7a93a-146">Enter **BloggingModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="7a93a-147">**Entity Data Model ウィザード**が起動します。</span><span class="sxs-lookup"><span data-stu-id="7a93a-147">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="7a93a-148">**[データベースから生成]** を選択し、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7a93a-148">Select **Generate from Database** and click **Next**</span></span>

    ![ウィザードの手順 1.](~/ef6/media/wizardstep1.png)

-   <span data-ttu-id="7a93a-150">最初のセクションで作成したデータベースへの接続を選択し、接続文字列の名前として「 **Bのログインコンテキスト**」と入力して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7a93a-150">Select the connection to the database you created in the first section, enter **BloggingContext** as the name of the connection string and click **Next**</span></span>

    ![ウィザードの手順 2.](~/ef6/media/wizardstep2.png)

-   <span data-ttu-id="7a93a-152">[テーブル] の横にあるチェックボックスをオンにしてすべてのテーブルをインポートし、[完了] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7a93a-152">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![ウィザードの手順 3.](~/ef6/media/wizardstep3.png)

 

<span data-ttu-id="7a93a-154">リバースエンジニアリングプロセスが完了すると、新しいモデルがプロジェクトに追加され、Entity Framework Designer で表示できるように開かれます。</span><span class="sxs-lookup"><span data-stu-id="7a93a-154">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="7a93a-155">App.config ファイルも、データベースの接続の詳細と共にプロジェクトに追加されています。</span><span class="sxs-lookup"><span data-stu-id="7a93a-155">An App.config file has also been added to your project with the connection details for the database.</span></span>

![モデルの初期](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="7a93a-157">Visual Studio 2010 の追加手順</span><span class="sxs-lookup"><span data-stu-id="7a93a-157">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="7a93a-158">Visual Studio 2010 で作業している場合、Entity Framework の最新バージョンにアップグレードするには、いくつかの追加の手順に従う必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a93a-158">If you are working in Visual Studio 2010 there are some additional steps you need to follow to upgrade to the latest version of Entity Framework.</span></span> <span data-ttu-id="7a93a-159">アップグレードは、強化された API サーフェイスにアクセスできるようにするために重要です。これは、より使いやすく、最新のバグ修正にも使用できます。</span><span class="sxs-lookup"><span data-stu-id="7a93a-159">Upgrading is important because it gives you access to an improved API surface, that is much easier to use, as well as the latest bug fixes.</span></span>

<span data-ttu-id="7a93a-160">まず、NuGet から最新バージョンの Entity Framework を取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a93a-160">First up, we need to get the latest version of Entity Framework from NuGet.</span></span>

-   <span data-ttu-id="7a93a-161">**プロジェクト-&gt; NuGet パッケージの管理...** 
     ***[nuget パッケージの管理...]** オプションがない場合は、[最新バージョンの nuget](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)をインストールする必要があり*ます。</span><span class="sxs-lookup"><span data-stu-id="7a93a-161">**Project –&gt; Manage NuGet Packages…**
*If you don’t have the **Manage NuGet Packages…** option you should install the [latest version of NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)*</span></span>
-   <span data-ttu-id="7a93a-162">**[オンライン]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="7a93a-162">Select the **Online** tab</span></span>
-   <span data-ttu-id="7a93a-163">**Entityframework**パッケージを選択します</span><span class="sxs-lookup"><span data-stu-id="7a93a-163">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="7a93a-164">**[インストール]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7a93a-164">Click **Install**</span></span>

<span data-ttu-id="7a93a-165">次に、モデルをスワップして、新しいバージョンの Entity Framework で導入された DbContext API を使用するコードを生成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a93a-165">Next, we need to swap our model to generate code that makes use of the DbContext API, which was introduced in later versions of Entity Framework.</span></span>

-   <span data-ttu-id="7a93a-166">EF デザイナーでモデルの空の場所を右クリックし、 **[コード生成項目の追加...]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7a93a-166">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="7a93a-167">左側のメニューから **[オンラインテンプレート]** を選択し、 **dbcontext**を検索します。</span><span class="sxs-lookup"><span data-stu-id="7a93a-167">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="7a93a-168">**C @ no__t の EF 5.X DbContext ジェネレーター**を選択し、名前として「 **b model** 」と入力して、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7a93a-168">Select the EF **5.x DbContext Generator for C\#**, enter **BloggingModel** as the name and click **Add**</span></span>

    ![DbContext テンプレート](~/ef6/media/dbcontexttemplate.png)

 

## <a name="4-reading--writing-data"></a><span data-ttu-id="7a93a-170">4。データの読み取り & 書き込み</span><span class="sxs-lookup"><span data-stu-id="7a93a-170">4. Reading & Writing Data</span></span>

<span data-ttu-id="7a93a-171">モデルを作成したので、これを使用していくつかのデータにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="7a93a-171">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="7a93a-172">データへのアクセスに使用するクラスは、EDMX ファイルに基づいて自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="7a93a-172">The classes we are going to use to access data are being automatically generated for you based on the EDMX file.</span></span>

<span data-ttu-id="7a93a-173">*このスクリーンショットは Visual Studio 2012 からのものです。 Visual Studio 2010 を使用している場合は、BloggingModel.tt ファイルと BloggingModel.Context.tt ファイルは、EDMX ファイルの下に入れ子になっているのではなく、プロジェクトの直下に配置されます。*</span><span class="sxs-lookup"><span data-stu-id="7a93a-173">*This screen shot is from Visual Studio 2012, if you are using Visual Studio 2010 the BloggingModel.tt and BloggingModel.Context.tt files will be directly under your project rather than nested under the EDMX file.*</span></span>

![生成されたクラス DF](~/ef6/media/generatedclassesdf.png)

 

<span data-ttu-id="7a93a-175">次に示すように、Program.cs に Main メソッドを実装します。</span><span class="sxs-lookup"><span data-stu-id="7a93a-175">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="7a93a-176">このコードは、コンテキストの新しいインスタンスを作成し、それを使用して新しいブログを挿入します。</span><span class="sxs-lookup"><span data-stu-id="7a93a-176">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="7a93a-177">次に、LINQ クエリを使用して、データベースのすべてのブログをタイトル別にアルファベット順に並べ替えて取得します。</span><span class="sxs-lookup"><span data-stu-id="7a93a-177">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

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

<span data-ttu-id="7a93a-178">これで、アプリケーションを実行してテストできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="7a93a-178">You can now run the application and test it out.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
 

## <a name="5-dealing-with-database-changes"></a><span data-ttu-id="7a93a-179">5。データベースの変更に対処する</span><span class="sxs-lookup"><span data-stu-id="7a93a-179">5. Dealing with Database Changes</span></span>

<span data-ttu-id="7a93a-180">次に、データベーススキーマをいくつか変更します。これらの変更を行うときは、これらの変更を反映するようにモデルを更新する必要もあります。</span><span class="sxs-lookup"><span data-stu-id="7a93a-180">Now it’s time to make some changes to our database schema, when we make these changes we also need to update our model to reflect those changes.</span></span>

<span data-ttu-id="7a93a-181">最初の手順では、データベーススキーマにいくつかの変更を行います。</span><span class="sxs-lookup"><span data-stu-id="7a93a-181">The first step is to make some changes to the database schema.</span></span> <span data-ttu-id="7a93a-182">ここでは、ユーザーテーブルをスキーマに追加します。</span><span class="sxs-lookup"><span data-stu-id="7a93a-182">We’re going to add a Users table to the schema.</span></span>

-   <span data-ttu-id="7a93a-183">サーバーエクスプローラーで**Databasefirst. ブログ**データベースを右クリックし、 **[新しいクエリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7a93a-183">Right-click on the **DatabaseFirst.Blogging** database in Server Explorer and select **New Query**</span></span>
-   <span data-ttu-id="7a93a-184">次の SQL を新しいクエリにコピーし、クエリを右クリックして、 **[実行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7a93a-184">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

``` SQL
CREATE TABLE [dbo].[Users]
(
    [Username] NVARCHAR(50) NOT NULL PRIMARY KEY,  
    [DisplayName] NVARCHAR(MAX) NULL
)
```

<span data-ttu-id="7a93a-185">スキーマが更新されたので、これらの変更によってモデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="7a93a-185">Now that the schema is updated, it’s time to update the model with those changes.</span></span>

-   <span data-ttu-id="7a93a-186">EF デザイナーでモデルの空の場所を右クリックし、[データベースからモデルを更新...] を選択すると、更新ウィザードが起動します。</span><span class="sxs-lookup"><span data-stu-id="7a93a-186">Right-click on an empty spot of your model in the EF Designer and select ‘Update Model from Database…’, this will launch the Update Wizard</span></span>
-   <span data-ttu-id="7a93a-187">更新ウィザードの [追加] タブで、[テーブル] の横にあるチェックボックスをオンにします。これは、スキーマから新しいテーブルを追加することを示します。</span><span class="sxs-lookup"><span data-stu-id="7a93a-187">On the Add tab of the Update Wizard check the box next to Tables, this indicates that we want to add any new tables from the schema.</span></span>
    <span data-ttu-id="7a93a-188">*The タブには、更新中に変更がチェックされる、モデル内の既存のテーブルが表示されます。[削除] タブには、スキーマから削除されたテーブルが表示され、更新の一部としてモデルからも削除されます。これら2つのタブの情報は自動的に検出され、情報提供のみを目的として提供されます。*</span><span class="sxs-lookup"><span data-stu-id="7a93a-188">*The Refresh tab shows any existing tables in the model that will be checked for changes during the update. The Delete tabs show any tables that have been removed from the schema and will also be removed from the model as part of the update. The information on these two tabs is automatically detected and is provided for informational purposes only, you cannot change any settings.*</span></span>

    ![更新ウィザード](~/ef6/media/refreshwizard.png)

-   <span data-ttu-id="7a93a-190">更新ウィザードで [完了] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7a93a-190">Click Finish on the Update Wizard</span></span>

 

<span data-ttu-id="7a93a-191">これで、モデルが更新され、データベースに追加したユーザーテーブルにマップされる新しいユーザーエンティティが追加されました。</span><span class="sxs-lookup"><span data-stu-id="7a93a-191">The model is now updated to include a new User entity that maps to the Users table we added to the database.</span></span>

![更新されたモデル](~/ef6/media/modelupdated.png)

## <a name="summary"></a><span data-ttu-id="7a93a-193">まとめ</span><span class="sxs-lookup"><span data-stu-id="7a93a-193">Summary</span></span>

<span data-ttu-id="7a93a-194">このチュートリアルでは Database First 開発について説明しました。これにより、既存のデータベースに基づいて EF デザイナーでモデルを作成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="7a93a-194">In this walkthrough we looked at Database First development, which allowed us to create a model in the EF Designer based on an existing database.</span></span> <span data-ttu-id="7a93a-195">次に、そのモデルを使用して、データベースのデータの読み取りと書き込みを行います。</span><span class="sxs-lookup"><span data-stu-id="7a93a-195">We then used that model to read and write some data from the database.</span></span> <span data-ttu-id="7a93a-196">最後に、データベーススキーマに加えられた変更を反映するようにモデルを更新しました。</span><span class="sxs-lookup"><span data-stu-id="7a93a-196">Finally, we updated the model to reflect changes we made to the database schema.</span></span>
