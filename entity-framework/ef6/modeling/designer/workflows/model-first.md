---
title: Model First-EF6
description: Entity Framework 6 の Model First
author: divega
ms.date: 10/23/2016
ms.assetid: e1b9c319-bb8a-4417-ac94-7890f257e7f6
uid: ef6/modeling/designer/workflows/model-first
ms.openlocfilehash: e995072f0f4ac23b755acc193719e5571e18f544
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620406"
---
# <a name="model-first"></a><span data-ttu-id="c5cbd-103">Model First</span><span class="sxs-lookup"><span data-stu-id="c5cbd-103">Model First</span></span>
<span data-ttu-id="c5cbd-104">このビデオとステップバイステップのチュートリアルでは、Entity Framework を使用した Model First 開発の概要について説明します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-104">This video and step-by-step walkthrough provide an introduction to Model First development using Entity Framework.</span></span> <span data-ttu-id="c5cbd-105">Model First を使用すると、Entity Framework Designer を使用して新しいモデルを作成し、モデルからデータベーススキーマを生成できます。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-105">Model First allows you to create a new model using the Entity Framework Designer and then generate a database schema from the model.</span></span> <span data-ttu-id="c5cbd-106">モデルは EDMX ファイル (.edmx 拡張子) に格納されており、Entity Framework Designer で表示および編集できます。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-106">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="c5cbd-107">アプリケーションで対話するクラスは、EDMX ファイルから自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-107">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="c5cbd-108">ビデオを見る</span><span class="sxs-lookup"><span data-stu-id="c5cbd-108">Watch the video</span></span>
<span data-ttu-id="c5cbd-109">このビデオとステップバイステップのチュートリアルでは、Entity Framework を使用した Model First 開発の概要について説明します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-109">This video and step-by-step walkthrough provide an introduction to Model First development using Entity Framework.</span></span> <span data-ttu-id="c5cbd-110">Model First を使用すると、Entity Framework Designer を使用して新しいモデルを作成し、モデルからデータベーススキーマを生成できます。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-110">Model First allows you to create a new model using the Entity Framework Designer and then generate a database schema from the model.</span></span> <span data-ttu-id="c5cbd-111">モデルは EDMX ファイル (.edmx 拡張子) に格納されており、Entity Framework Designer で表示および編集できます。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-111">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="c5cbd-112">アプリケーションで対話するクラスは、EDMX ファイルから自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-112">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

<span data-ttu-id="c5cbd-113">**提供**: [Rowan Miller](https://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="c5cbd-113">**Presented By**: [Rowan Miller](https://romiller.com/)</span></span>

<span data-ttu-id="c5cbd-114">**ビデオ**: [WMV](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.wmv)  |  [MP4](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v)  |  [WMV (ZIP)](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)</span><span class="sxs-lookup"><span data-stu-id="c5cbd-114">**Video**: [WMV](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.wmv) | [MP4](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="c5cbd-115">前提条件</span><span class="sxs-lookup"><span data-stu-id="c5cbd-115">Pre-Requisites</span></span>

<span data-ttu-id="c5cbd-116">このチュートリアルを完了するには、Visual Studio 2010 または Visual Studio 2012 がインストールされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-116">You will need to have Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="c5cbd-117">Visual Studio 2010 を使用している場合は、 [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) もインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-117">If you are using Visual Studio 2010, you will also need to have [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

## <a name="1-create-the-application"></a><span data-ttu-id="c5cbd-118">1. アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="c5cbd-118">1. Create the Application</span></span>

<span data-ttu-id="c5cbd-119">単純にするために、Model First を使用してデータアクセスを実行する基本的なコンソールアプリケーションを構築します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-119">To keep things simple we’re going to build a basic console application that uses the Model First to perform data access:</span></span>

-   <span data-ttu-id="c5cbd-120">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="c5cbd-120">Open Visual Studio</span></span>
-   <span data-ttu-id="c5cbd-121">**ファイル- &gt; 新規 &gt; プロジェクト...**</span><span class="sxs-lookup"><span data-stu-id="c5cbd-121">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="c5cbd-122">左側のメニューと**コンソールアプリケーション**から [ **Windows** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-122">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="c5cbd-123">名前として **Modelfirstsample** を入力します</span><span class="sxs-lookup"><span data-stu-id="c5cbd-123">Enter **ModelFirstSample** as the name</span></span>
-   <span data-ttu-id="c5cbd-124">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-124">Select **OK**</span></span>

## <a name="2-create-model"></a><span data-ttu-id="c5cbd-125">2. モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="c5cbd-125">2. Create Model</span></span>

<span data-ttu-id="c5cbd-126">ここでは、Visual Studio の一部として含まれている Entity Framework Designer を使用して、モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-126">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="c5cbd-127">**プロジェクト- &gt; 新しい項目の追加...**</span><span class="sxs-lookup"><span data-stu-id="c5cbd-127">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="c5cbd-128">左側のメニューから [ **データ** ] を選択し、[ADO.NET] をクリックし **Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="c5cbd-128">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="c5cbd-129">名前として「 **Bloggingmodel** 」と入力し、[ **OK**] をクリックすると、Entity Data Model ウィザードが起動します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-129">Enter **BloggingModel** as the name and click **OK**, this launches the Entity Data Model Wizard</span></span>
-   <span data-ttu-id="c5cbd-130">**空のモデル**を選択し、[**完了**] をクリックします</span><span class="sxs-lookup"><span data-stu-id="c5cbd-130">Select **Empty Model** and click **Finish**</span></span>

    ![空のモデルの作成](~/ef6/media/createemptymodel.png)

<span data-ttu-id="c5cbd-132">空のモデルを使用して Entity Framework Designer が開かれています。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-132">The Entity Framework Designer is opened with a blank model.</span></span> <span data-ttu-id="c5cbd-133">これで、モデルへのエンティティ、プロパティ、およびアソシエーションの追加を開始できます。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-133">Now we can start adding entities, properties and associations to the model.</span></span>

-   <span data-ttu-id="c5cbd-134">デザイン画面を右クリックし、[**プロパティ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-134">Right-click on the design surface and select **Properties**</span></span>
-   <span data-ttu-id="c5cbd-135">[**エンティティコンテナー名**] を**bプロパティウィンドウコンテキスト**に変更します。 
     *これは、生成される派生コンテキストの名前です。コンテキストは、データベースとのセッションを表し、データのクエリと保存を行うこと*ができます。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-135">In the Properties window change the **Entity Container Name** to **BloggingContext**
*This is the name of the derived context that will be generated for you, the context represents a session with the database, allowing us to query and save data*</span></span>
-   <span data-ttu-id="c5cbd-136">デザイン画面を右クリックし、[**新しい &gt; エンティティの追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-136">Right-click on the design surface and select **Add New -&gt; Entity…**</span></span>
-   <span data-ttu-id="c5cbd-137">エンティティ名として「ブログ」を、キー名として **「** **Blog** **id** 」を入力し、[OK] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-137">Enter **Blog** as the entity name and **BlogId** as the key name and click **OK**</span></span>

    ![ブログエンティティの追加](~/ef6/media/addblogentity.png)

-   <span data-ttu-id="c5cbd-139">デザイン画面で新しいエンティティを右クリックし、[ **新しい &gt; スカラープロパティの追加**] を選択し、プロパティの名前として「 **name** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-139">Right-click on the new entity on the design surface and select **Add New -&gt; Scalar Property**, enter **Name** as the name of the property.</span></span>
-   <span data-ttu-id="c5cbd-140">**Url**プロパティを追加するには、この手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-140">Repeat this process to add a **Url** property.</span></span>
-   <span data-ttu-id="c5cbd-141">デザイン画面で [ **url** ] プロパティを右クリックし、[**プロパティ**] を選択します。 [プロパティウィンドウ [ **null 許容**] 設定を [ **True**] に変更します。これにより、ブログに 
     *url を割り当てずにデータベースにブログを保存できるようになり*ます。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-141">Right-click on the **Url** property on the design surface and select **Properties**, in the Properties window change the **Nullable** setting to **True**
*This allows us to save a Blog to the database without assigning it a Url*</span></span>
-   <span data-ttu-id="c5cbd-142">習得した手法を使用して、 **PostId** key プロパティを持つ**Post**エンティティを追加します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-142">Using the techniques you just learnt, add a **Post** entity with a **PostId** key property</span></span>
-   <span data-ttu-id="c5cbd-143">**Post**エンティティに**タイトル**と**コンテンツ**のスカラープロパティを追加する</span><span class="sxs-lookup"><span data-stu-id="c5cbd-143">Add **Title** and **Content** scalar properties to the **Post** entity</span></span>

<span data-ttu-id="c5cbd-144">いくつかのエンティティを作成したので、次はそれらの間にアソシエーション (またはリレーションシップ) を追加します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-144">Now that we have a couple of entities, it’s time to add an association (or relationship) between them.</span></span>

-   <span data-ttu-id="c5cbd-145">デザイン画面を右クリックし、[**新しい &gt; 関連付けの追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-145">Right-click on the design surface and select **Add New -&gt; Association…**</span></span>
-   <span data-ttu-id="c5cbd-146">リレーションシップの一方の端点を、多重度が**1**で、もう一方のエンドポイントが複数要素の接続性**Many\*\*\*\*を持つ\*\*\*\*ブログ**になるようにします。 
     *これは、ブログに多数の投稿があり、投稿が1つのブログに属していることを意味*します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-146">Make one end of the relationship point to **Blog** with a multiplicity of **One** and the other end point to **Post** with a multiplicity of **Many**
*This means that a Blog has many Posts and a Post belongs to one Blog*</span></span>
-   <span data-ttu-id="c5cbd-147">[**外部キーのプロパティを ' Post ' エンティティに追加する**] チェックボックスがオンになっていることを確認し、[ **OK** ] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-147">Ensure the **Add foreign key properties to 'Post' Entity** box is checked and click **OK**</span></span>

    ![関連付けの追加 (MF)](~/ef6/media/addassociationmf.png)

<span data-ttu-id="c5cbd-149">ここでは、データベースを生成してデータの読み書きに使用できる単純なモデルを用意しました。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-149">We now have a simple model that we can generate a database from and use to read and write data.</span></span>

![モデルの初期](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="c5cbd-151">Visual Studio 2010 の追加手順</span><span class="sxs-lookup"><span data-stu-id="c5cbd-151">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="c5cbd-152">Visual Studio 2010 で作業している場合、Entity Framework の最新バージョンにアップグレードするには、いくつかの追加の手順に従う必要があります。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-152">If you are working in Visual Studio 2010 there are some additional steps you need to follow to upgrade to the latest version of Entity Framework.</span></span> <span data-ttu-id="c5cbd-153">アップグレードは、強化された API サーフェイスにアクセスできるようにするために重要です。これは、より使いやすく、最新のバグ修正にも使用できます。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-153">Upgrading is important because it gives you access to an improved API surface, that is much easier to use, as well as the latest bug fixes.</span></span>

<span data-ttu-id="c5cbd-154">まず、NuGet から最新バージョンの Entity Framework を取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-154">First up, we need to get the latest version of Entity Framework from NuGet.</span></span>

-   <span data-ttu-id="c5cbd-155">**プロジェクト– &gt;NuGet パッケージの管理...** 
     *[ **Nuget パッケージの管理...** ] オプションがない場合は、[最新バージョンの nuget](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)をインストールする必要があり*ます。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-155">**Project –&gt; Manage NuGet Packages…**
*If you don’t have the **Manage NuGet Packages…** option you should install the [latest version of NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)*</span></span>
-   <span data-ttu-id="c5cbd-156">[ **オンライン** ] タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-156">Select the **Online** tab</span></span>
-   <span data-ttu-id="c5cbd-157">**Entityframework**パッケージを選択します</span><span class="sxs-lookup"><span data-stu-id="c5cbd-157">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="c5cbd-158">**[Install]** (インストール) をクリックします。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-158">Click **Install**</span></span>

<span data-ttu-id="c5cbd-159">次に、モデルをスワップして、新しいバージョンの Entity Framework で導入された DbContext API を使用するコードを生成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-159">Next, we need to swap our model to generate code that makes use of the DbContext API, which was introduced in later versions of Entity Framework.</span></span>

-   <span data-ttu-id="c5cbd-160">EF デザイナーでモデルの空の場所を右クリックし、[**コード生成項目の追加...** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-160">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="c5cbd-161">左側のメニューから [**オンラインテンプレート**] を選択し、 **dbcontext**を検索します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-161">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="c5cbd-162">**C \# の EF 5.X Dbcontext ジェネレーター**を選択し、名前として「 **bの出力モデル**」と入力して、[**追加**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-162">Select the EF **5.x DbContext Generator for C\#**, enter **BloggingModel** as the name and click **Add**</span></span>

    ![DbContext テンプレート](~/ef6/media/dbcontexttemplate.png)

## <a name="3-generating-the-database"></a><span data-ttu-id="c5cbd-164">3. データベースを生成する</span><span class="sxs-lookup"><span data-stu-id="c5cbd-164">3. Generating the Database</span></span>

<span data-ttu-id="c5cbd-165">モデルを使用すると、Entity Framework は、モデルを使用してデータの格納と取得を行うことができるデータベーススキーマを計算できます。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-165">Given our model, Entity Framework can calculate a database schema that will allow us to store and retrieve data using the model.</span></span>

<span data-ttu-id="c5cbd-166">Visual Studio と共にインストールされるデータベースサーバーは、インストールされている Visual Studio のバージョンによって異なります。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-166">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="c5cbd-167">Visual Studio 2010 を使用している場合は、SQL Express データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-167">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="c5cbd-168">Visual Studio 2012 を使用している場合は、 [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-168">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) database.</span></span>

<span data-ttu-id="c5cbd-169">では、データベースを生成してみましょう。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-169">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="c5cbd-170">デザイン画面を右クリックし、[**モデルからデータベースを生成...** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-170">Right-click on the design surface and select **Generate Database from Model…**</span></span>
-   <span data-ttu-id="c5cbd-171">[**新しい接続**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-171">Click **New Connection…**</span></span> <span data-ttu-id="c5cbd-172">使用している Visual Studio のバージョンに応じて、LocalDB または SQL Express のいずれかを指定し、データベース名として「 **Modelfirst** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-172">and specify either LocalDB or SQL Express, depending on which version of Visual Studio you are using, enter **ModelFirst.Blogging** as the database name.</span></span>

    ![LocalDB 接続の MF](~/ef6/media/localdbconnectionmf.png)

    ![Sql Express 接続の MF](~/ef6/media/sqlexpressconnectionmf.png)

-   <span data-ttu-id="c5cbd-175">[ **OK]** を選択すると、新しいデータベースを作成するかどうかを確認するメッセージが表示されます。 [**はい**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-175">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="c5cbd-176">[ **次へ** ] を選択すると、データベーススキーマを作成するスクリプトが Entity Framework Designer によって計算されます。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-176">Select **Next** and the Entity Framework Designer will calculate a script to create the database schema</span></span>
-   <span data-ttu-id="c5cbd-177">スクリプトが表示されたら、[ **完了** ] をクリックすると、スクリプトがプロジェクトに追加されて開かれます。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-177">Once the script is displayed, click **Finish** and the script will be added to your project and opened</span></span>
-   <span data-ttu-id="c5cbd-178">スクリプトを右クリックして [ **実行**] を選択すると、接続先のデータベースを指定するように求められます。使用している Visual Studio のバージョンに応じて、LocalDB または SQL Server Express を指定します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-178">Right-click on the script and select **Execute**, you will be prompted to specify the database to connect to, specify LocalDB or SQL Server Express, depending on which version of Visual Studio you are using</span></span>

## <a name="4-reading--writing-data"></a><span data-ttu-id="c5cbd-179">4. データの読み取り & 書き込み</span><span class="sxs-lookup"><span data-stu-id="c5cbd-179">4. Reading & Writing Data</span></span>

<span data-ttu-id="c5cbd-180">モデルを作成したので、これを使用していくつかのデータにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-180">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="c5cbd-181">データへのアクセスに使用するクラスは、EDMX ファイルに基づいて自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-181">The classes we are going to use to access data are being automatically generated for you based on the EDMX file.</span></span>

<span data-ttu-id="c5cbd-182">*このスクリーンショットは Visual Studio 2012 からのものです。 Visual Studio 2010 を使用している場合は、BloggingModel.tt ファイルと BloggingModel.Context.tt ファイルは、EDMX ファイルの下に入れ子になっているのではなく、プロジェクトの直下に配置されます。*</span><span class="sxs-lookup"><span data-stu-id="c5cbd-182">*This screen shot is from Visual Studio 2012, if you are using Visual Studio 2010 the BloggingModel.tt and BloggingModel.Context.tt files will be directly under your project rather than nested under the EDMX file.*</span></span>

![生成されたクラス](~/ef6/media/generatedclasses.png)

<span data-ttu-id="c5cbd-184">次に示すように、Program.cs に Main メソッドを実装します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-184">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="c5cbd-185">このコードは、コンテキストの新しいインスタンスを作成し、それを使用して新しいブログを挿入します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-185">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="c5cbd-186">次に、LINQ クエリを使用して、データベースのすべてのブログをタイトル別にアルファベット順に並べ替えて取得します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-186">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

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

<span data-ttu-id="c5cbd-187">これで、アプリケーションを実行してテストできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-187">You can now run the application and test it out.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```

## <a name="5-dealing-with-model-changes"></a><span data-ttu-id="c5cbd-188">5. モデルの変更を処理する</span><span class="sxs-lookup"><span data-stu-id="c5cbd-188">5. Dealing with Model Changes</span></span>

<span data-ttu-id="c5cbd-189">次に、モデルに何らかの変更を加えてみましょう。これらの変更を行うときは、データベーススキーマも更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-189">Now it’s time to make some changes to our model, when we make these changes we also need to update the database schema.</span></span>

<span data-ttu-id="c5cbd-190">まず、モデルに新しいユーザーエンティティを追加します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-190">We’ll start by adding a new User entity to our model.</span></span>

-   <span data-ttu-id="c5cbd-191">キーの名前と**文字列**としてユーザー**名**を持つ新しい**ユーザー**エンティティ名を、キーのプロパティの型として追加します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-191">Add a new **User** entity name with **Username** as the key name and **String** as the property type for the key</span></span>

    ![ユーザーエンティティの追加](~/ef6/media/adduserentity.png)

-   <span data-ttu-id="c5cbd-193">デザイン画面で [**ユーザー名**] プロパティを右クリックし、 **[プロパティ**] を選択します。プロパティウィンドウ、[ **MaxLength** ] 設定を**50**に変更します。これに 
     *より、ユーザー名に格納できるデータが50文字に制限さ*れます。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-193">Right-click on the **Username** property on the design surface and select **Properties**, In the Properties window change the **MaxLength** setting to **50**
*This restricts the data that can be stored in username to 50 characters*</span></span>
-   <span data-ttu-id="c5cbd-194">**DisplayName**スカラープロパティを**User**エンティティに追加する</span><span class="sxs-lookup"><span data-stu-id="c5cbd-194">Add a **DisplayName** scalar property to the **User** entity</span></span>

<span data-ttu-id="c5cbd-195">これで、モデルが更新され、新しいユーザーエンティティ型に対応するようにデータベースを更新する準備ができました。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-195">We now have an updated model and we are ready to update the database to accommodate our new User entity type.</span></span>

-   <span data-ttu-id="c5cbd-196">デザイン画面を右クリックし、[ **モデルからデータベースを生成**] を選択すると、Entity Framework によって、更新されたモデルに基づいてスキーマを再作成するスクリプトが計算されます。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-196">Right-click on the design surface and select **Generate Database from Model…**, Entity Framework will calculate a script to recreate a schema based on the updated model.</span></span>
-   <span data-ttu-id="c5cbd-197">**[完了]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-197">Click **Finish**</span></span>
-   <span data-ttu-id="c5cbd-198">既存の DDL スクリプトとモデルのマッピングおよびストレージ部分の上書きに関する警告が表示される場合は、両方の警告に対して [ **はい** ] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-198">You may receive warnings about overwriting the existing DDL script and the mapping and storage parts of the model, click **Yes** for both these warnings</span></span>
-   <span data-ttu-id="c5cbd-199">データベースを作成するための更新された SQL スクリプトが開きます</span><span class="sxs-lookup"><span data-stu-id="c5cbd-199">The updated SQL script to create the database is opened for you</span></span>  
    <span data-ttu-id="c5cbd-200">*生成されたスクリプトは、既存のすべてのテーブルを削除してから、スキーマを最初から再作成します。これは、ローカルでの開発では機能しますが、既に配置されているデータベースに変更をプッシュする場合には有効ではありません。既に配置されているデータベースに変更を発行する必要がある場合は、スクリプトを編集するか、スキーマ比較ツールを使用して移行スクリプトを計算する必要があります。*</span><span class="sxs-lookup"><span data-stu-id="c5cbd-200">*The script that is generated will drop all existing tables and then recreate the schema from scratch. This may work for local development but is not a viable for pushing changes to a database that has already been deployed. If you need to publish changes to a database that has already been deployed, you will need to edit the script or use a schema compare tool to calculate a migration script.*</span></span>
-   <span data-ttu-id="c5cbd-201">スクリプトを右クリックして [ **実行**] を選択すると、接続先のデータベースを指定するように求められます。使用している Visual Studio のバージョンに応じて、LocalDB または SQL Server Express を指定します。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-201">Right-click on the script and select **Execute**, you will be prompted to specify the database to connect to, specify LocalDB or SQL Server Express, depending on which version of Visual Studio you are using</span></span>

## <a name="summary"></a><span data-ttu-id="c5cbd-202">要約</span><span class="sxs-lookup"><span data-stu-id="c5cbd-202">Summary</span></span>

<span data-ttu-id="c5cbd-203">このチュートリアルでは Model First の開発について説明しました。これにより、EF デザイナーでモデルを作成し、そのモデルからデータベースを生成することができました。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-203">In this walkthrough we looked at Model First development, which allowed us to create a model in the EF Designer and then generate a database from that model.</span></span> <span data-ttu-id="c5cbd-204">次に、モデルを使用して、データベースのデータの読み取りと書き込みを行います。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-204">We then used the model to read and write some data from the database.</span></span> <span data-ttu-id="c5cbd-205">最後に、モデルを更新した後、モデルに一致するようにデータベーススキーマを再作成しました。</span><span class="sxs-lookup"><span data-stu-id="c5cbd-205">Finally, we updated the model and then recreated the database schema to match the model.</span></span>
