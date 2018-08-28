---
title: 最初に EF6 をモデル化します。
author: divega
ms.date: 2016-10-23
ms.assetid: e1b9c319-bb8a-4417-ac94-7890f257e7f6
ms.openlocfilehash: c21592b27fa752532f5ede5923d0bd751f0bf372
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42998116"
---
# <a name="model-first"></a><span data-ttu-id="529e0-102">まずモデルします。</span><span class="sxs-lookup"><span data-stu-id="529e0-102">Model First</span></span>
<span data-ttu-id="529e0-103">このビデオ、およびステップ バイ ステップ チュートリアルでは、Entity Framework を使用して Model First の開発の概要を提供します。</span><span class="sxs-lookup"><span data-stu-id="529e0-103">This video and step-by-step walkthrough provide an introduction to Model First development using Entity Framework.</span></span> <span data-ttu-id="529e0-104">まず、モデルを使用すると、Entity Framework デザイナーを使用して、新しいモデルを作成し、モデルからデータベース スキーマを生成できます。</span><span class="sxs-lookup"><span data-stu-id="529e0-104">Model First allows you to create a new model using the Entity Framework Designer and then generate a database schema from the model.</span></span> <span data-ttu-id="529e0-105">モデルは、EDMX ファイル (拡張子は .edmx) に格納され、表示および編集できます、Entity Framework デザイナーで。</span><span class="sxs-lookup"><span data-stu-id="529e0-105">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="529e0-106">アプリケーションと対話するクラスは、EDMX ファイルから自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="529e0-106">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="529e0-107">ビデオを見る</span><span class="sxs-lookup"><span data-stu-id="529e0-107">Watch the video</span></span>
<span data-ttu-id="529e0-108">このビデオ、およびステップ バイ ステップ チュートリアルでは、Entity Framework を使用して Model First の開発の概要を提供します。</span><span class="sxs-lookup"><span data-stu-id="529e0-108">This video and step-by-step walkthrough provide an introduction to Model First development using Entity Framework.</span></span> <span data-ttu-id="529e0-109">まず、モデルを使用すると、Entity Framework デザイナーを使用して、新しいモデルを作成し、モデルからデータベース スキーマを生成できます。</span><span class="sxs-lookup"><span data-stu-id="529e0-109">Model First allows you to create a new model using the Entity Framework Designer and then generate a database schema from the model.</span></span> <span data-ttu-id="529e0-110">モデルは、EDMX ファイル (拡張子は .edmx) に格納され、表示および編集できます、Entity Framework デザイナーで。</span><span class="sxs-lookup"><span data-stu-id="529e0-110">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="529e0-111">アプリケーションと対話するクラスは、EDMX ファイルから自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="529e0-111">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

<span data-ttu-id="529e0-112">**提供**: [Rowan Miller](http://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="529e0-112">**Presented By**: [Rowan Miller](http://romiller.com/)</span></span>

<span data-ttu-id="529e0-113">**ビデオ**: [WMV](http://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.wmv) | [MP4](http://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)</span><span class="sxs-lookup"><span data-stu-id="529e0-113">**Video**: [WMV](http://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.wmv) | [MP4](http://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="529e0-114">前提条件</span><span class="sxs-lookup"><span data-stu-id="529e0-114">Pre-Requisites</span></span>

<span data-ttu-id="529e0-115">Visual Studio 2010 を使用する必要がありますか、このチュートリアルを実行する Visual Studio 2012 がインストールされています。</span><span class="sxs-lookup"><span data-stu-id="529e0-115">You will need to have Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="529e0-116">Visual Studio 2010 を使用している場合も必要になりますが[NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="529e0-116">If you are using Visual Studio 2010, you will also need to have [NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

## <a name="1-create-the-application"></a><span data-ttu-id="529e0-117">1.アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="529e0-117">1. Create the Application</span></span>

<span data-ttu-id="529e0-118">複雑にならない Model First を使用して、データ アクセスを実行する基本的なコンソール アプリケーションをビルドしていきます。</span><span class="sxs-lookup"><span data-stu-id="529e0-118">To keep things simple we’re going to build a basic console application that uses the Model First to perform data access:</span></span>

-   <span data-ttu-id="529e0-119">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="529e0-119">Open Visual Studio</span></span>
-   <span data-ttu-id="529e0-120">**ファイル -&gt;新機能 -&gt;プロジェクト.**</span><span class="sxs-lookup"><span data-stu-id="529e0-120">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="529e0-121">選択**Windows**左側のメニューと**コンソール アプリケーション**</span><span class="sxs-lookup"><span data-stu-id="529e0-121">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="529e0-122">入力**ModelFirstSample**名として</span><span class="sxs-lookup"><span data-stu-id="529e0-122">Enter **ModelFirstSample** as the name</span></span>
-   <span data-ttu-id="529e0-123">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="529e0-123">Select **OK**</span></span>

## <a name="2-create-model"></a><span data-ttu-id="529e0-124">2.モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="529e0-124">2. Create Model</span></span>

<span data-ttu-id="529e0-125">Visual Studio の一部として含まれている Entity Framework デザイナーを使用してモデルを作成することになります。</span><span class="sxs-lookup"><span data-stu-id="529e0-125">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="529e0-126">**プロジェクト -&gt;新しい項目を追加しています.**</span><span class="sxs-lookup"><span data-stu-id="529e0-126">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="529e0-127">選択**データ**左側のメニューをクリックし**ADO.NET Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="529e0-127">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="529e0-128">入力**BloggingModel**名をクリックします**OK**、Entity Data Model ウィザードが起動</span><span class="sxs-lookup"><span data-stu-id="529e0-128">Enter **BloggingModel** as the name and click **OK**, this launches the Entity Data Model Wizard</span></span>
-   <span data-ttu-id="529e0-129">選択**空のモデル**クリック**完了**</span><span class="sxs-lookup"><span data-stu-id="529e0-129">Select **Empty Model** and click **Finish**</span></span>

    ![CreateEmptyModel](~/ef6/media/createemptymodel.png)

<span data-ttu-id="529e0-131">Entity Framework のデザイナーは、空のモデルで開かれます。</span><span class="sxs-lookup"><span data-stu-id="529e0-131">The Entity Framework Designer is opened with a blank model.</span></span> <span data-ttu-id="529e0-132">今すぐ、モデルにエンティティ、プロパティ、およびアソシエーションを追加するを開始できます。</span><span class="sxs-lookup"><span data-stu-id="529e0-132">Now we can start adding entities, properties and associations to the model.</span></span>

-   <span data-ttu-id="529e0-133">デザイン サーフェスと選択を右クリックして**プロパティ**</span><span class="sxs-lookup"><span data-stu-id="529e0-133">Right-click on the design surface and select **Properties**</span></span>
-   <span data-ttu-id="529e0-134">プロパティ ウィンドウの変更で、**エンティティ コンテナー名**に**BloggingContext**
    *コンテキストを生成される派生コンテキストの名前ですクエリを実行し、データを保存することができます、データベースとのセッションを表します*</span><span class="sxs-lookup"><span data-stu-id="529e0-134">In the Properties window change the **Entity Container Name** to **BloggingContext**
*This is the name of the derived context that will be generated for you, the context represents a session with the database, allowing us to query and save data*</span></span>
-   <span data-ttu-id="529e0-135">デザイン サーフェスと選択を右クリックして **- 新しい追加&gt;エンティティ.**</span><span class="sxs-lookup"><span data-stu-id="529e0-135">Right-click on the design surface and select **Add New -&gt; Entity…**</span></span>
-   <span data-ttu-id="529e0-136">入力**ブログ**エンティティ名としてと**BlogId**キー名をクリックします**OK**</span><span class="sxs-lookup"><span data-stu-id="529e0-136">Enter **Blog** as the entity name and **BlogId** as the key name and click **OK**</span></span>

    ![AddBlogEntity](~/ef6/media/addblogentity.png)

-   <span data-ttu-id="529e0-138">デザイン サーフェスと選択の新しいエンティティを右クリックして **- 新しい追加&gt;スカラー プロパティ**、入力**名前**プロパティの名前として。</span><span class="sxs-lookup"><span data-stu-id="529e0-138">Right-click on the new entity on the design surface and select **Add New -&gt; Scalar Property**, enter **Name** as the name of the property.</span></span>
-   <span data-ttu-id="529e0-139">追加するには、このプロセスを繰り返して、 **Url**プロパティ。</span><span class="sxs-lookup"><span data-stu-id="529e0-139">Repeat this process to add a **Url** property.</span></span>
-   <span data-ttu-id="529e0-140">右クリックし、 **Url**プロパティをデザイン画面を選択します**プロパティ**、プロパティ ウィンドウの変更で、 **Nullable**設定**をTrue**
    *これにより、Url を代入せず、ブログをデータベースに保存するには*</span><span class="sxs-lookup"><span data-stu-id="529e0-140">Right-click on the **Url** property on the design surface and select **Properties**, in the Properties window change the **Nullable** setting to **True**
*This allows us to save a Blog to the database without assigning it a Url*</span></span>
-   <span data-ttu-id="529e0-141">追加習得する手法を使用して、 **Post**を持つエンティティを**投稿 Id**キー プロパティ</span><span class="sxs-lookup"><span data-stu-id="529e0-141">Using the techniques you just learnt, add a **Post** entity with a **PostId** key property</span></span>
-   <span data-ttu-id="529e0-142">追加**タイトル**と**コンテンツ**スカラー プロパティを**Post**エンティティ</span><span class="sxs-lookup"><span data-stu-id="529e0-142">Add **Title** and **Content** scalar properties to the **Post** entity</span></span>

<span data-ttu-id="529e0-143">あるので、いくつかのエンティティ、それらの間の関連付け (またはリレーションシップ) を追加する時間を勧めします。</span><span class="sxs-lookup"><span data-stu-id="529e0-143">Now that we have a couple of entities, it’s time to add an association (or relationship) between them.</span></span>

-   <span data-ttu-id="529e0-144">デザイン サーフェスと選択を右クリックして **- 新しい追加&gt;関連しています.**</span><span class="sxs-lookup"><span data-stu-id="529e0-144">Right-click on the design surface and select **Add New -&gt; Association…**</span></span>
-   <span data-ttu-id="529e0-145">リレーションシップの一方の端を指すように**ブログ**の多重度を持つ**1 つ**およびその他の終点を**Post**の多重度を持つ**多く** 
    *つまりブログは多くの投稿、および、1 つのブログに投稿が属しています。*</span><span class="sxs-lookup"><span data-stu-id="529e0-145">Make one end of the relationship point to **Blog** with a multiplicity of **One** and the other end point to **Post** with a multiplicity of **Many**
*This means that a Blog has many Posts and a Post belongs to one Blog*</span></span>
-   <span data-ttu-id="529e0-146">確認、 **'Post' エンティティに外部キー プロパティを追加**ボックスがオンになってし、 **OK**</span><span class="sxs-lookup"><span data-stu-id="529e0-146">Ensure the **Add foreign key properties to 'Post' Entity** box is checked and click **OK**</span></span>

    ![AddAssociationMF](~/ef6/media/addassociationmf.png)

<span data-ttu-id="529e0-148">データベースを生成してデータを読み書きするを使用して、単純なモデルがあるようになりました。</span><span class="sxs-lookup"><span data-stu-id="529e0-148">We now have a simple model that we can generate a database from and use to read and write data.</span></span>

![ModelInitial](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="529e0-150">Visual Studio 2010 で追加の手順</span><span class="sxs-lookup"><span data-stu-id="529e0-150">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="529e0-151">Visual Studio 2010 で作業している場合は、いくつか追加の手順を次の Entity Framework の最新バージョンにアップグレードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="529e0-151">If you are working in Visual Studio 2010 there are some additional steps you need to follow to upgrade to the latest version of Entity Framework.</span></span> <span data-ttu-id="529e0-152">それにアクセスできる最新のバグ修正と強化された API へのアクセスを簡単に使用されるため、アップグレードすることが重要です。</span><span class="sxs-lookup"><span data-stu-id="529e0-152">Upgrading is important because it gives you access to an improved API surface, that is much easier to use, as well as the latest bug fixes.</span></span>

<span data-ttu-id="529e0-153">まず、NuGet から最新バージョンの Entity Framework を取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="529e0-153">First up, we need to get the latest version of Entity Framework from NuGet.</span></span>

-   <span data-ttu-id="529e0-154">**プロジェクト:&gt; NuGet パッケージを管理しています.** 
    *していない場合、 **NuGet パッケージの管理.** オプションをインストールする必要があります、 [NuGet の最新バージョン](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)*</span><span class="sxs-lookup"><span data-stu-id="529e0-154">**Project –&gt; Manage NuGet Packages…**
*If you don’t have the **Manage NuGet Packages…** option you should install the [latest version of NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)*</span></span>
-   <span data-ttu-id="529e0-155">選択、**オンライン** タブ</span><span class="sxs-lookup"><span data-stu-id="529e0-155">Select the **Online** tab</span></span>
-   <span data-ttu-id="529e0-156">選択、 **EntityFramework**パッケージ</span><span class="sxs-lookup"><span data-stu-id="529e0-156">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="529e0-157">クリックして**インストール**</span><span class="sxs-lookup"><span data-stu-id="529e0-157">Click **Install**</span></span>

<span data-ttu-id="529e0-158">次に、Entity Framework の以降のバージョンで導入された DbContext API を使用するコードを生成するには、このモデルを交換する必要があります。</span><span class="sxs-lookup"><span data-stu-id="529e0-158">Next, we need to swap our model to generate code that makes use of the DbContext API, which was introduced in later versions of Entity Framework.</span></span>

-   <span data-ttu-id="529e0-159">EF Designer でのモデルの空いている場所を右クリックして**コード生成項目の追加.**</span><span class="sxs-lookup"><span data-stu-id="529e0-159">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="529e0-160">選択**オンライン テンプレート**、左側のメニューと検索から**DbContext**</span><span class="sxs-lookup"><span data-stu-id="529e0-160">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="529e0-161">EF の選択**5.x C 用 DbContext ジェネレーター\#**、入力**BloggingModel**名をクリックします**追加**</span><span class="sxs-lookup"><span data-stu-id="529e0-161">Select the EF **5.x DbContext Generator for C\#**, enter **BloggingModel** as the name and click **Add**</span></span>

    ![DbContextTemplate](~/ef6/media/dbcontexttemplate.png)

## <a name="3-generating-the-database"></a><span data-ttu-id="529e0-163">3.データベースの生成</span><span class="sxs-lookup"><span data-stu-id="529e0-163">3. Generating the Database</span></span>

<span data-ttu-id="529e0-164">私たちのモデルを指定するには、Entity Framework は、データ モデルを使用して格納および取得することを許可するデータベース スキーマを計算できます。</span><span class="sxs-lookup"><span data-stu-id="529e0-164">Given our model, Entity Framework can calculate a database schema that will allow us to store and retrieve data using the model.</span></span>

<span data-ttu-id="529e0-165">Visual Studio と共にインストールされているデータベース サーバーは、インストールした Visual Studio のバージョンによって異なります。</span><span class="sxs-lookup"><span data-stu-id="529e0-165">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="529e0-166">Visual Studio 2010 を使用している場合は、SQL Express データベースを作成するがします。</span><span class="sxs-lookup"><span data-stu-id="529e0-166">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="529e0-167">Visual Studio 2012 を使用しているかどうかは、作成する、 [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx)データベース。</span><span class="sxs-lookup"><span data-stu-id="529e0-167">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) database.</span></span>

<span data-ttu-id="529e0-168">データベースを生成してみましょう。</span><span class="sxs-lookup"><span data-stu-id="529e0-168">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="529e0-169">デザイン サーフェスと選択を右クリックして**モデルからデータベースを生成しています.**</span><span class="sxs-lookup"><span data-stu-id="529e0-169">Right-click on the design surface and select **Generate Database from Model…**</span></span>
-   <span data-ttu-id="529e0-170">クリックして**新しい接続.**</span><span class="sxs-lookup"><span data-stu-id="529e0-170">Click **New Connection…**</span></span> <span data-ttu-id="529e0-171">LocalDB または SQL Express は、Visual Studio のバージョンに応じて使用しているのいずれかを指定して入力**ModelFirst.Blogging**としてデータベース名。</span><span class="sxs-lookup"><span data-stu-id="529e0-171">and specify either LocalDB or SQL Express, depending on which version of Visual Studio you are using, enter **ModelFirst.Blogging** as the database name.</span></span>

    ![LocalDBConnectionMF](~/ef6/media/localdbconnectionmf.png)

    ![SqlExpressConnectionMF](~/ef6/media/sqlexpressconnectionmf.png)

-   <span data-ttu-id="529e0-174">選択**OK**かどうかは、新しいデータベースを作成するように要求がある **[はい]**</span><span class="sxs-lookup"><span data-stu-id="529e0-174">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="529e0-175">選択**次**と、Entity Framework デザイナーでは、データベース スキーマを作成するスクリプトを計算します。</span><span class="sxs-lookup"><span data-stu-id="529e0-175">Select **Next** and the Entity Framework Designer will calculate a script to create the database schema</span></span>
-   <span data-ttu-id="529e0-176">スクリプトが表示されたら、クリックして**完了**とスクリプトがプロジェクトに追加し、開く</span><span class="sxs-lookup"><span data-stu-id="529e0-176">Once the script is displayed, click **Finish** and the script will be added to your project and opened</span></span>
-   <span data-ttu-id="529e0-177">クリックし、スクリプトを右クリックして**Execute**LocalDB を指定するのには、接続するデータベースを指定を求め、または SQL Server Express は、Visual Studio のバージョンに応じて使用しています。</span><span class="sxs-lookup"><span data-stu-id="529e0-177">Right-click on the script and select **Execute**, you will be prompted to specify the database to connect to, specify LocalDB or SQL Server Express, depending on which version of Visual Studio you are using</span></span>

## <a name="4-reading--writing-data"></a><span data-ttu-id="529e0-178">4.読み取りとデータの書き込み</span><span class="sxs-lookup"><span data-stu-id="529e0-178">4. Reading & Writing Data</span></span>

<span data-ttu-id="529e0-179">使用して一部のデータにアクセスするには、モデルができました。</span><span class="sxs-lookup"><span data-stu-id="529e0-179">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="529e0-180">クラスは、ここでを使用するデータにアクセスされている自動的に生成される EDMX ファイルに基づいて。</span><span class="sxs-lookup"><span data-stu-id="529e0-180">The classes we are going to use to access data are being automatically generated for you based on the EDMX file.</span></span>

<span data-ttu-id="529e0-181">*このスクリーン ショットは Visual Studio 2012、Visual Studio 2010 を使用している場合、BloggingModel.tt、BloggingModel.Context.tt ファイル、プロジェクトの下に直接ではなく EDMX ファイルの下で入れ子になった。*</span><span class="sxs-lookup"><span data-stu-id="529e0-181">*This screen shot is from Visual Studio 2012, if you are using Visual Studio 2010 the BloggingModel.tt and BloggingModel.Context.tt files will be directly under your project rather than nested under the EDMX file.*</span></span>

![GeneratedClasses](~/ef6/media/generatedclasses.png)

<span data-ttu-id="529e0-183">次に示すように、Program.cs の Main メソッドを実装します。</span><span class="sxs-lookup"><span data-stu-id="529e0-183">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="529e0-184">このコードは、コンテキストの新しいインスタンスを作成、し、それを使用して、新しいブログを挿入します。</span><span class="sxs-lookup"><span data-stu-id="529e0-184">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="529e0-185">タイトルでアルファベット順では、データベースからすべてのブログを取得するのに LINQ クエリを使用します。</span><span class="sxs-lookup"><span data-stu-id="529e0-185">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

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

<span data-ttu-id="529e0-186">アプリケーションを実行し、テストを実行できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="529e0-186">You can now run the application and test it out.</span></span>

```
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```

## <a name="5-dealing-with-model-changes"></a><span data-ttu-id="529e0-187">5.モデルの変更を処理します。</span><span class="sxs-lookup"><span data-stu-id="529e0-187">5. Dealing with Model Changes</span></span>

<span data-ttu-id="529e0-188">ここには、データベース スキーマを更新する必要もありますこれらの変更を行ったときに、モデルでは、変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="529e0-188">Now it’s time to make some changes to our model, when we make these changes we also need to update the database schema.</span></span>

<span data-ttu-id="529e0-189">私たちのモデルに新しいユーザー エンティティを追加することから始めます。</span><span class="sxs-lookup"><span data-stu-id="529e0-189">We’ll start by adding a new User entity to our model.</span></span>

-   <span data-ttu-id="529e0-190">新しい追加**ユーザー**でエンティティ名**Username** 、キー名としてと**文字列**キーのプロパティの型として</span><span class="sxs-lookup"><span data-stu-id="529e0-190">Add a new **User** entity name with **Username** as the key name and **String** as the property type for the key</span></span>

    ![AddUserEntity](~/ef6/media/adduserentity.png)

-   <span data-ttu-id="529e0-192">右クリックし、 **Username**プロパティをデザイン画面を選択します**プロパティ**のプロパティ ウィンドウの変更、 **MaxLength**設定**50** 
    *これは 50 文字にユーザー名で格納できるデータを制限*</span><span class="sxs-lookup"><span data-stu-id="529e0-192">Right-click on the **Username** property on the design surface and select **Properties**, In the Properties window change the **MaxLength** setting to **50**
*This restricts the data that can be stored in username to 50 characters*</span></span>
-   <span data-ttu-id="529e0-193">追加、 **DisplayName**スカラー プロパティを**ユーザー**エンティティ</span><span class="sxs-lookup"><span data-stu-id="529e0-193">Add a **DisplayName** scalar property to the **User** entity</span></span>

<span data-ttu-id="529e0-194">今すぐ更新済みのモデルがあるし、新しいユーザー エンティティ型の対応するためにデータベースを更新する準備ができました。</span><span class="sxs-lookup"><span data-stu-id="529e0-194">We now have an updated model and we are ready to update the database to accommodate our new User entity type.</span></span>

-   <span data-ttu-id="529e0-195">デザイン サーフェスと選択を右クリックして**モデルからデータベースを生成しています.**、Entity Framework は、更新されたモデルに基づくスキーマを再作成するスクリプトを計算します。</span><span class="sxs-lookup"><span data-stu-id="529e0-195">Right-click on the design surface and select **Generate Database from Model…**, Entity Framework will calculate a script to recreate a schema based on the updated model.</span></span>
-   <span data-ttu-id="529e0-196">クリックして**完了**</span><span class="sxs-lookup"><span data-stu-id="529e0-196">Click **Finish**</span></span>
-   <span data-ttu-id="529e0-197">既存の DDL スクリプトとモデルのマッピングと記憶域部分の上書きに関する警告が表示される、 をクリックする可能性があります**はい**これら両方の警告</span><span class="sxs-lookup"><span data-stu-id="529e0-197">You may receive warnings about overwriting the existing DDL script and the mapping and storage parts of the model, click **Yes** for both these warnings</span></span>
-   <span data-ttu-id="529e0-198">ためにデータベースを作成する更新された SQL スクリプトを開く</span><span class="sxs-lookup"><span data-stu-id="529e0-198">The updated SQL script to create the database is opened for you</span></span>  
    <span data-ttu-id="529e0-199">*生成されるスクリプトでは、すべての既存のテーブルをドロップしを最初からスキーマを再作成します。これにより、ローカル開発用動作可能性がありますが、既にデプロイされているデータベースに変更をプッシュの実行可能なではありません。既にデプロイされているデータベースに変更を発行する必要がある場合は、スクリプトを編集またはスキーマ比較ツールを使用して、移行スクリプトを計算する必要があります。*</span><span class="sxs-lookup"><span data-stu-id="529e0-199">*The script that is generated will drop all existing tables and then recreate the schema from scratch. This may work for local development but is not a viable for pushing changes to a database that has already been deployed. If you need to publish changes to a database that has already been deployed, you will need to edit the script or use a schema compare tool to calculate a migration script.*</span></span>
-   <span data-ttu-id="529e0-200">クリックし、スクリプトを右クリックして**Execute**LocalDB を指定するのには、接続するデータベースを指定を求め、または SQL Server Express は、Visual Studio のバージョンに応じて使用しています。</span><span class="sxs-lookup"><span data-stu-id="529e0-200">Right-click on the script and select **Execute**, you will be prompted to specify the database to connect to, specify LocalDB or SQL Server Express, depending on which version of Visual Studio you are using</span></span>

## <a name="summary"></a><span data-ttu-id="529e0-201">まとめ</span><span class="sxs-lookup"><span data-stu-id="529e0-201">Summary</span></span>

<span data-ttu-id="529e0-202">このチュートリアルでは、Model First の開発を見たのでですることができますが EF デザイナーでモデルを作成し、そのモデルからデータベースを生成します。</span><span class="sxs-lookup"><span data-stu-id="529e0-202">In this walkthrough we looked at Model First development, which allowed us to create a model in the EF Designer and then generate a database from that model.</span></span> <span data-ttu-id="529e0-203">モデルを使用して、データベースからいくつかのデータを読み書きします。</span><span class="sxs-lookup"><span data-stu-id="529e0-203">We then used the model to read and write some data from the database.</span></span> <span data-ttu-id="529e0-204">最後に、モデルを更新し、モデルと一致するデータベース スキーマを再作成します。</span><span class="sxs-lookup"><span data-stu-id="529e0-204">Finally, we updated the model and then recreated the database schema to match the model.</span></span>
