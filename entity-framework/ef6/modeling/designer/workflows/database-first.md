---
title: データベースの最初の EF6
author: divega
ms.date: 2016-10-23
ms.assetid: cc6ffdb3-388d-4e79-a201-01ec2577c949
ms.openlocfilehash: 93ae5729e487ed9be3972ac78d599dbea19ed458
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2018
ms.locfileid: "44251090"
---
# <a name="database-first"></a><span data-ttu-id="c9915-102">最初のデータベースします。</span><span class="sxs-lookup"><span data-stu-id="c9915-102">Database First</span></span>
<span data-ttu-id="c9915-103">このビデオ、およびステップ バイ ステップ チュートリアルでは、Entity Framework を使用して Database First の開発の概要を提供します。</span><span class="sxs-lookup"><span data-stu-id="c9915-103">This video and step-by-step walkthrough provide an introduction to Database First development using Entity Framework.</span></span> <span data-ttu-id="c9915-104">データベース最初にすることができますをリバース エンジニア リング、既存のデータベースからモデル。</span><span class="sxs-lookup"><span data-stu-id="c9915-104">Database First allows you to reverse engineer a model from an existing database.</span></span> <span data-ttu-id="c9915-105">モデルは、EDMX ファイル (拡張子は .edmx) に格納され、表示および編集できます、Entity Framework デザイナーで。</span><span class="sxs-lookup"><span data-stu-id="c9915-105">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="c9915-106">アプリケーションと対話するクラスは、EDMX ファイルから自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="c9915-106">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="c9915-107">ビデオを見る</span><span class="sxs-lookup"><span data-stu-id="c9915-107">Watch the video</span></span>
<span data-ttu-id="c9915-108">このビデオでは、Entity Framework を使用して Database First の開発の概要を提供します。</span><span class="sxs-lookup"><span data-stu-id="c9915-108">This video provides an introduction to Database First development using Entity Framework.</span></span> <span data-ttu-id="c9915-109">データベース最初にすることができますをリバース エンジニア リング、既存のデータベースからモデル。</span><span class="sxs-lookup"><span data-stu-id="c9915-109">Database First allows you to reverse engineer a model from an existing database.</span></span> <span data-ttu-id="c9915-110">モデルは、EDMX ファイル (拡張子は .edmx) に格納され、表示および編集できます、Entity Framework デザイナーで。</span><span class="sxs-lookup"><span data-stu-id="c9915-110">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="c9915-111">アプリケーションと対話するクラスは、EDMX ファイルから自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="c9915-111">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

<span data-ttu-id="c9915-112">**提供**: [Rowan Miller](http://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="c9915-112">**Presented By**: [Rowan Miller](http://romiller.com/)</span></span>

<span data-ttu-id="c9915-113">**ビデオ**: [WMV](http://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv) | [MP4](http://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)</span><span class="sxs-lookup"><span data-stu-id="c9915-113">**Video**: [WMV](http://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv) | [MP4](http://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="c9915-114">前提条件</span><span class="sxs-lookup"><span data-stu-id="c9915-114">Pre-Requisites</span></span>

<span data-ttu-id="c9915-115">少なくとも Visual Studio 2010 が必要がありますか、このチュートリアルを実行する Visual Studio 2012 がインストールされています。</span><span class="sxs-lookup"><span data-stu-id="c9915-115">You will need to have at least Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="c9915-116">Visual Studio 2010 を使用している場合も必要になりますが[NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="c9915-116">If you are using Visual Studio 2010, you will also need to have [NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

 

## <a name="1-create-an-existing-database"></a><span data-ttu-id="c9915-117">1.既存のデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="c9915-117">1. Create an Existing Database</span></span>

<span data-ttu-id="c9915-118">通常それは既に作成されて、既存のデータベースを対象としているときに、このチュートリアルではアクセスするデータベースを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c9915-118">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="c9915-119">Visual Studio と共にインストールされているデータベース サーバーは、インストールした Visual Studio のバージョンによって異なります。</span><span class="sxs-lookup"><span data-stu-id="c9915-119">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="c9915-120">Visual Studio 2010 を使用している場合は、SQL Express データベースを作成するがします。</span><span class="sxs-lookup"><span data-stu-id="c9915-120">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="c9915-121">Visual Studio 2012 を使用しているかどうかは、作成する、 [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx)データベース。</span><span class="sxs-lookup"><span data-stu-id="c9915-121">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) database.</span></span>

 

<span data-ttu-id="c9915-122">データベースを生成してみましょう。</span><span class="sxs-lookup"><span data-stu-id="c9915-122">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="c9915-123">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="c9915-123">Open Visual Studio</span></span>
-   <span data-ttu-id="c9915-124">**ビュー -&gt;サーバー エクスプ ローラー**</span><span class="sxs-lookup"><span data-stu-id="c9915-124">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="c9915-125">右クリックして**データ接続 -&gt;接続の追加.**</span><span class="sxs-lookup"><span data-stu-id="c9915-125">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="c9915-126">場合、まだデータベースに接続して、サーバー エクスプ ローラーから前に、データ ソースとして Microsoft SQL Server を選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c9915-126">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![データ ソースの選択](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="c9915-128">LocalDB または SQL Express をインストールしたものに応じてのいずれかに接続し、入力**DatabaseFirst.Blogging**データベース名として</span><span class="sxs-lookup"><span data-stu-id="c9915-128">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **DatabaseFirst.Blogging** as the database name</span></span>

    ![Sql Express 接続 DF](~/ef6/media/sqlexpressconnectiondf.png)

    ![LocalDB 接続 DF](~/ef6/media/localdbconnectiondf.png)

-   <span data-ttu-id="c9915-131">選択**OK**かどうかは、新しいデータベースを作成するように要求がある **[はい]**</span><span class="sxs-lookup"><span data-stu-id="c9915-131">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![[データベース] ダイアログを作成します。](~/ef6/media/createdatabasedialog.png)

-   <span data-ttu-id="c9915-133">新しいデータベースがサーバー エクスプ ローラーに表示されますを右クリックして選択**新しいクエリ**</span><span class="sxs-lookup"><span data-stu-id="c9915-133">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="c9915-134">新しいクエリに、次の SQL をコピーし、クエリを選択します右クリックし、 **Execute**</span><span class="sxs-lookup"><span data-stu-id="c9915-134">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

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

## <a name="2-create-the-application"></a><span data-ttu-id="c9915-135">2.アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="c9915-135">2. Create the Application</span></span>

<span data-ttu-id="c9915-136">単純化するには、Database First を使用して、データ アクセスを実行する基本的なコンソール アプリケーションをビルドしていきます。</span><span class="sxs-lookup"><span data-stu-id="c9915-136">To keep things simple we’re going to build a basic console application that uses the Database First to perform data access:</span></span>

-   <span data-ttu-id="c9915-137">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="c9915-137">Open Visual Studio</span></span>
-   <span data-ttu-id="c9915-138">**ファイル -&gt;新機能 -&gt;プロジェクト.**</span><span class="sxs-lookup"><span data-stu-id="c9915-138">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="c9915-139">選択**Windows**左側のメニューと**コンソール アプリケーション**</span><span class="sxs-lookup"><span data-stu-id="c9915-139">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="c9915-140">入力**DatabaseFirstSample**名として</span><span class="sxs-lookup"><span data-stu-id="c9915-140">Enter **DatabaseFirstSample** as the name</span></span>
-   <span data-ttu-id="c9915-141">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c9915-141">Select **OK**</span></span>

 

## <a name="3-reverse-engineer-model"></a><span data-ttu-id="c9915-142">3.リバース エンジニア リング モデル</span><span class="sxs-lookup"><span data-stu-id="c9915-142">3. Reverse Engineer Model</span></span>

<span data-ttu-id="c9915-143">Visual Studio の一部として含まれている Entity Framework デザイナーを使用してモデルを作成することになります。</span><span class="sxs-lookup"><span data-stu-id="c9915-143">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="c9915-144">**プロジェクト -&gt;新しい項目を追加しています.**</span><span class="sxs-lookup"><span data-stu-id="c9915-144">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="c9915-145">選択**データ**左側のメニューをクリックし**ADO.NET Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="c9915-145">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="c9915-146">入力**BloggingModel**名をクリックします**OK**</span><span class="sxs-lookup"><span data-stu-id="c9915-146">Enter **BloggingModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="c9915-147">これにより、起動、 **Entity Data Model ウィザード**</span><span class="sxs-lookup"><span data-stu-id="c9915-147">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="c9915-148">選択**データベースから生成**クリック **[次へ]**</span><span class="sxs-lookup"><span data-stu-id="c9915-148">Select **Generate from Database** and click **Next**</span></span>

    ![ウィザードの手順 1](~/ef6/media/wizardstep1.png)

-   <span data-ttu-id="c9915-150">最初のセクションで作成したデータベース接続を選択して、入力**BloggingContext**をクリックして、接続文字列の名前として **[次へ]**</span><span class="sxs-lookup"><span data-stu-id="c9915-150">Select the connection to the database you created in the first section, enter **BloggingContext** as the name of the connection string and click **Next**</span></span>

    ![ウィザードの手順 2](~/ef6/media/wizardstep2.png)

-   <span data-ttu-id="c9915-152">すべてのテーブルをインポートし、[完了] をクリックします。 'テーブル' の横にあるチェック ボックスをクリックします。</span><span class="sxs-lookup"><span data-stu-id="c9915-152">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![ウィザードの手順 3](~/ef6/media/wizardstep3.png)

 

<span data-ttu-id="c9915-154">リバース エンジニア リング プロセスが完了すると、新しいモデルがプロジェクトに追加しを開いて、Entity Framework デザイナーで表示することです。</span><span class="sxs-lookup"><span data-stu-id="c9915-154">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="c9915-155">App.config ファイルが、データベースの接続詳細を含むプロジェクトにも追加されました。</span><span class="sxs-lookup"><span data-stu-id="c9915-155">An App.config file has also been added to your project with the connection details for the database.</span></span>

![初期モデルします。](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="c9915-157">Visual Studio 2010 で追加の手順</span><span class="sxs-lookup"><span data-stu-id="c9915-157">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="c9915-158">Visual Studio 2010 で作業している場合は、いくつか追加の手順を次の Entity Framework の最新バージョンにアップグレードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="c9915-158">If you are working in Visual Studio 2010 there are some additional steps you need to follow to upgrade to the latest version of Entity Framework.</span></span> <span data-ttu-id="c9915-159">それにアクセスできる最新のバグ修正と強化された API へのアクセスを簡単に使用されるため、アップグレードすることが重要です。</span><span class="sxs-lookup"><span data-stu-id="c9915-159">Upgrading is important because it gives you access to an improved API surface, that is much easier to use, as well as the latest bug fixes.</span></span>

<span data-ttu-id="c9915-160">まず、NuGet から最新バージョンの Entity Framework を取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c9915-160">First up, we need to get the latest version of Entity Framework from NuGet.</span></span>

-   <span data-ttu-id="c9915-161">**プロジェクト:&gt; NuGet パッケージを管理しています.** 
    *していない場合、 **NuGet パッケージの管理.** オプションをインストールする必要があります、 [NuGet の最新バージョン](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)*</span><span class="sxs-lookup"><span data-stu-id="c9915-161">**Project –&gt; Manage NuGet Packages…**
*If you don’t have the **Manage NuGet Packages…** option you should install the [latest version of NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)*</span></span>
-   <span data-ttu-id="c9915-162">選択、**オンライン** タブ</span><span class="sxs-lookup"><span data-stu-id="c9915-162">Select the **Online** tab</span></span>
-   <span data-ttu-id="c9915-163">選択、 **EntityFramework**パッケージ</span><span class="sxs-lookup"><span data-stu-id="c9915-163">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="c9915-164">クリックして**インストール**</span><span class="sxs-lookup"><span data-stu-id="c9915-164">Click **Install**</span></span>

<span data-ttu-id="c9915-165">次に、Entity Framework の以降のバージョンで導入された DbContext API を使用するコードを生成するには、このモデルを交換する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c9915-165">Next, we need to swap our model to generate code that makes use of the DbContext API, which was introduced in later versions of Entity Framework.</span></span>

-   <span data-ttu-id="c9915-166">EF Designer でのモデルの空いている場所を右クリックして**コード生成項目の追加.**</span><span class="sxs-lookup"><span data-stu-id="c9915-166">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="c9915-167">選択**オンライン テンプレート**、左側のメニューと検索から**DbContext**</span><span class="sxs-lookup"><span data-stu-id="c9915-167">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="c9915-168">EF の選択**5.x C 用 DbContext ジェネレーター\#**、入力**BloggingModel**名をクリックします**追加**</span><span class="sxs-lookup"><span data-stu-id="c9915-168">Select the EF **5.x DbContext Generator for C\#**, enter **BloggingModel** as the name and click **Add**</span></span>

    ![DbContext テンプレート](~/ef6/media/dbcontexttemplate.png)

 

## <a name="4-reading--writing-data"></a><span data-ttu-id="c9915-170">4.読み取りとデータの書き込み</span><span class="sxs-lookup"><span data-stu-id="c9915-170">4. Reading & Writing Data</span></span>

<span data-ttu-id="c9915-171">使用して一部のデータにアクセスするには、モデルができました。</span><span class="sxs-lookup"><span data-stu-id="c9915-171">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="c9915-172">クラスは、ここでを使用するデータにアクセスされている自動的に生成される EDMX ファイルに基づいて。</span><span class="sxs-lookup"><span data-stu-id="c9915-172">The classes we are going to use to access data are being automatically generated for you based on the EDMX file.</span></span>

<span data-ttu-id="c9915-173">*このスクリーン ショットは Visual Studio 2012、Visual Studio 2010 を使用している場合、BloggingModel.tt、BloggingModel.Context.tt ファイル、プロジェクトの下に直接ではなく EDMX ファイルの下で入れ子になった。*</span><span class="sxs-lookup"><span data-stu-id="c9915-173">*This screen shot is from Visual Studio 2012, if you are using Visual Studio 2010 the BloggingModel.tt and BloggingModel.Context.tt files will be directly under your project rather than nested under the EDMX file.*</span></span>

![生成されたクラス DF](~/ef6/media/generatedclassesdf.png)

 

<span data-ttu-id="c9915-175">次に示すように、Program.cs の Main メソッドを実装します。</span><span class="sxs-lookup"><span data-stu-id="c9915-175">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="c9915-176">このコードは、コンテキストの新しいインスタンスを作成、し、それを使用して、新しいブログを挿入します。</span><span class="sxs-lookup"><span data-stu-id="c9915-176">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="c9915-177">タイトルでアルファベット順では、データベースからすべてのブログを取得するのに LINQ クエリを使用します。</span><span class="sxs-lookup"><span data-stu-id="c9915-177">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

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

<span data-ttu-id="c9915-178">アプリケーションを実行し、テストを実行できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="c9915-178">You can now run the application and test it out.</span></span>

```
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
 

## <a name="5-dealing-with-database-changes"></a><span data-ttu-id="c9915-179">5.データベースの変更を処理します。</span><span class="sxs-lookup"><span data-stu-id="c9915-179">5. Dealing with Database Changes</span></span>

<span data-ttu-id="c9915-180">ここもそれらの変更を反映するように、モデルを更新する必要があります、これらの変更を行った場合、データベース スキーマをいくつか変更します。</span><span class="sxs-lookup"><span data-stu-id="c9915-180">Now it’s time to make some changes to our database schema, when we make these changes we also need to update our model to reflect those changes.</span></span>

<span data-ttu-id="c9915-181">最初の手順は、データベース スキーマを一部変更するのには。</span><span class="sxs-lookup"><span data-stu-id="c9915-181">The first step is to make some changes to the database schema.</span></span> <span data-ttu-id="c9915-182">ユーザー テーブルのスキーマを追加しようとしています。</span><span class="sxs-lookup"><span data-stu-id="c9915-182">We’re going to add a Users table to the schema.</span></span>

-   <span data-ttu-id="c9915-183">右クリックし、 **DatabaseFirst.Blogging**サーバー エクスプ ローラーでデータベースし、選択**新しいクエリ**</span><span class="sxs-lookup"><span data-stu-id="c9915-183">Right-click on the **DatabaseFirst.Blogging** database in Server Explorer and select **New Query**</span></span>
-   <span data-ttu-id="c9915-184">新しいクエリに、次の SQL をコピーし、クエリを選択します右クリックし、 **Execute**</span><span class="sxs-lookup"><span data-stu-id="c9915-184">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

``` SQL
CREATE TABLE [dbo].[Users]
(
    [Username] NVARCHAR(50) NOT NULL PRIMARY KEY,  
    [DisplayName] NVARCHAR(MAX) NULL
)
```

<span data-ttu-id="c9915-185">これで、スキーマを更新すると、それらの変更、モデルの更新に時間を勧めします。</span><span class="sxs-lookup"><span data-stu-id="c9915-185">Now that the schema is updated, it’s time to update the model with those changes.</span></span>

-   <span data-ttu-id="c9915-186">EF デザイナーでモデルおよび選択 'からモデルを更新しています... データベース' の空いている場所を右クリックし、この更新プログラム ウィザードが起動</span><span class="sxs-lookup"><span data-stu-id="c9915-186">Right-click on an empty spot of your model in the EF Designer and select ‘Update Model from Database…’, this will launch the Update Wizard</span></span>
-   <span data-ttu-id="c9915-187">テーブルの横のボックスの更新ウィザードのチェックの追加 タブで、スキーマから新しいテーブルを追加することを示します。</span><span class="sxs-lookup"><span data-stu-id="c9915-187">On the Add tab of the Update Wizard check the box next to Tables, this indicates that we want to add any new tables from the schema.</span></span>
    <span data-ttu-id="c9915-188">*[更新] タブでは、モデルの変更、更新中にチェックされる、既存のテーブルを示します。削除のタブは、スキーマから削除されているし、更新プログラムの一部として、モデルから削除もテーブルを表示します。これら 2 つのタブ上の情報は自動的に検出し、は情報提供だけを目的としており、設定を変更することはできません。*</span><span class="sxs-lookup"><span data-stu-id="c9915-188">*The Refresh tab shows any existing tables in the model that will be checked for changes during the update. The Delete tabs show any tables that have been removed from the schema and will also be removed from the model as part of the update. The information on these two tabs is automatically detected and is provided for informational purposes only, you cannot change any settings.*</span></span>

    ![ウィザードを更新します。](~/ef6/media/refreshwizard.png)

-   <span data-ttu-id="c9915-190">更新ウィザードで [完了] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="c9915-190">Click Finish on the Update Wizard</span></span>

 

<span data-ttu-id="c9915-191">モデルが更新され、データベースに追加するユーザー テーブルにマップする新しいユーザー エンティティを含めます。</span><span class="sxs-lookup"><span data-stu-id="c9915-191">The model is now updated to include a new User entity that maps to the Users table we added to the database.</span></span>

![モデルが更新されました](~/ef6/media/modelupdated.png)

## <a name="summary"></a><span data-ttu-id="c9915-193">まとめ</span><span class="sxs-lookup"><span data-stu-id="c9915-193">Summary</span></span>

<span data-ttu-id="c9915-194">このチュートリアルでは、Database First の開発を見たのでですることができます、既存のデータベースに基づく EF Designer でモデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="c9915-194">In this walkthrough we looked at Database First development, which allowed us to create a model in the EF Designer based on an existing database.</span></span> <span data-ttu-id="c9915-195">そのモデルを使用して、データベースからいくつかのデータを読み書きします。</span><span class="sxs-lookup"><span data-stu-id="c9915-195">We then used that model to read and write some data from the database.</span></span> <span data-ttu-id="c9915-196">最後に、データベース スキーマに加えられた変更を反映するようにモデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="c9915-196">Finally, we updated the model to reflect changes we made to the database schema.</span></span>
