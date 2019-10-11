---
title: Model First-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: e1b9c319-bb8a-4417-ac94-7890f257e7f6
ms.openlocfilehash: 1b37805beb3d33f0b6dad2577a8abb3ea8f7b1e4
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182439"
---
# <a name="model-first"></a><span data-ttu-id="3b1ae-102">Model First</span><span class="sxs-lookup"><span data-stu-id="3b1ae-102">Model First</span></span>
<span data-ttu-id="3b1ae-103">このビデオとステップバイステップのチュートリアルでは、Entity Framework を使用した Model First 開発の概要について説明します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-103">This video and step-by-step walkthrough provide an introduction to Model First development using Entity Framework.</span></span> <span data-ttu-id="3b1ae-104">Model First を使用すると、Entity Framework Designer を使用して新しいモデルを作成し、モデルからデータベーススキーマを生成できます。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-104">Model First allows you to create a new model using the Entity Framework Designer and then generate a database schema from the model.</span></span> <span data-ttu-id="3b1ae-105">モデルは EDMX ファイル (.edmx 拡張子) に格納されており、Entity Framework Designer で表示および編集できます。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-105">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="3b1ae-106">アプリケーションで対話するクラスは、EDMX ファイルから自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-106">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="3b1ae-107">ビデオを見る</span><span class="sxs-lookup"><span data-stu-id="3b1ae-107">Watch the video</span></span>
<span data-ttu-id="3b1ae-108">このビデオとステップバイステップのチュートリアルでは、Entity Framework を使用した Model First 開発の概要について説明します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-108">This video and step-by-step walkthrough provide an introduction to Model First development using Entity Framework.</span></span> <span data-ttu-id="3b1ae-109">Model First を使用すると、Entity Framework Designer を使用して新しいモデルを作成し、モデルからデータベーススキーマを生成できます。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-109">Model First allows you to create a new model using the Entity Framework Designer and then generate a database schema from the model.</span></span> <span data-ttu-id="3b1ae-110">モデルは EDMX ファイル (.edmx 拡張子) に格納されており、Entity Framework Designer で表示および編集できます。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-110">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="3b1ae-111">アプリケーションで対話するクラスは、EDMX ファイルから自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-111">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

<span data-ttu-id="3b1ae-112">**表示者**:[Rowan 明美](https://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="3b1ae-112">**Presented By**: [Rowan Miller](https://romiller.com/)</span></span>

<span data-ttu-id="3b1ae-113">**ビデオ**:[WMV](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.wmv) | [MP4](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)</span><span class="sxs-lookup"><span data-stu-id="3b1ae-113">**Video**: [WMV](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.wmv) | [MP4](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="3b1ae-114">前提条件</span><span class="sxs-lookup"><span data-stu-id="3b1ae-114">Pre-Requisites</span></span>

<span data-ttu-id="3b1ae-115">このチュートリアルを完了するには、Visual Studio 2010 または Visual Studio 2012 がインストールされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-115">You will need to have Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="3b1ae-116">Visual Studio 2010 を使用している場合は、 [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)もインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-116">If you are using Visual Studio 2010, you will also need to have [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

## <a name="1-create-the-application"></a><span data-ttu-id="3b1ae-117">1. アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="3b1ae-117">1. Create the Application</span></span>

<span data-ttu-id="3b1ae-118">単純にするために、Model First を使用してデータアクセスを実行する基本的なコンソールアプリケーションを構築します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-118">To keep things simple we’re going to build a basic console application that uses the Model First to perform data access:</span></span>

-   <span data-ttu-id="3b1ae-119">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="3b1ae-119">Open Visual Studio</span></span>
-   <span data-ttu-id="3b1ae-120">**ファイル-&gt; 新規-@no__t プロジェクト...**</span><span class="sxs-lookup"><span data-stu-id="3b1ae-120">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="3b1ae-121">左側のメニューと**コンソールアプリケーション**から **[Windows]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-121">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="3b1ae-122">名前として**Modelfirstsample**を入力します</span><span class="sxs-lookup"><span data-stu-id="3b1ae-122">Enter **ModelFirstSample** as the name</span></span>
-   <span data-ttu-id="3b1ae-123">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-123">Select **OK**</span></span>

## <a name="2-create-model"></a><span data-ttu-id="3b1ae-124">2. モデルの作成</span><span class="sxs-lookup"><span data-stu-id="3b1ae-124">2. Create Model</span></span>

<span data-ttu-id="3b1ae-125">ここでは、Visual Studio の一部として含まれている Entity Framework Designer を使用して、モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-125">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="3b1ae-126">**プロジェクト-&gt; 新しい項目の追加...**</span><span class="sxs-lookup"><span data-stu-id="3b1ae-126">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="3b1ae-127">左側のメニューから **データ** を選択し、ADO.NET をクリックし**Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="3b1ae-127">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="3b1ae-128">名前として「 **Bloggingmodel** 」と入力し、 **[OK]** をクリックすると、Entity Data Model ウィザードが起動します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-128">Enter **BloggingModel** as the name and click **OK**, this launches the Entity Data Model Wizard</span></span>
-   <span data-ttu-id="3b1ae-129">**空のモデル**を選択し、 **[完了]** をクリックします</span><span class="sxs-lookup"><span data-stu-id="3b1ae-129">Select **Empty Model** and click **Finish**</span></span>

    ![空のモデルの作成](~/ef6/media/createemptymodel.png)

<span data-ttu-id="3b1ae-131">空のモデルを使用して Entity Framework Designer が開かれています。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-131">The Entity Framework Designer is opened with a blank model.</span></span> <span data-ttu-id="3b1ae-132">これで、モデルへのエンティティ、プロパティ、およびアソシエーションの追加を開始できます。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-132">Now we can start adding entities, properties and associations to the model.</span></span>

-   <span data-ttu-id="3b1ae-133">デザイン画面を右クリックし、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-133">Right-click on the design surface and select **Properties**</span></span>
-   <span data-ttu-id="3b1ae-134">プロパティウィンドウ**エンティティコンテナー名**を**b@no__t context**に変更します。*これは、生成される派生コンテキストの名前です。コンテキストはデータベースとのセッションを表し、クエリを実行して保存することができます。データ*</span><span class="sxs-lookup"><span data-stu-id="3b1ae-134">In the Properties window change the **Entity Container Name** to **BloggingContext**
    *This is the name of the derived context that will be generated for you, the context represents a session with the database, allowing us to query and save data*</span></span>
-   <span data-ttu-id="3b1ae-135">デザイン画面を右クリックし、[ **Add New-&gt; Entity...** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-135">Right-click on the design surface and select **Add New -&gt; Entity…**</span></span>
-   <span data-ttu-id="3b1ae-136">エンティティ名として「ブログ」を、キー名として **「** **Blog** **id** 」を入力し、[OK] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-136">Enter **Blog** as the entity name and **BlogId** as the key name and click **OK**</span></span>

    ![ブログエンティティの追加](~/ef6/media/addblogentity.png)

-   <span data-ttu-id="3b1ae-138">デザイン画面で新しいエンティティを右クリックし、[追加] [**新しい &gt; スカラープロパティ**] の順に選択し、プロパティの名前として「 **name** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-138">Right-click on the new entity on the design surface and select **Add New -&gt; Scalar Property**, enter **Name** as the name of the property.</span></span>
-   <span data-ttu-id="3b1ae-139">**Url**プロパティを追加するには、この手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-139">Repeat this process to add a **Url** property.</span></span>
-   <span data-ttu-id="3b1ae-140">デザイン画面で **[url]** プロパティを右クリックし、 **[プロパティ]** を選択します。プロパティウィンドウ **[Null 許容]** 設定を**True**に変更し 
    *これにより、ブログに Url を割り当てずにデータベースにブログを保存できるようになります。*</span><span class="sxs-lookup"><span data-stu-id="3b1ae-140">Right-click on the **Url** property on the design surface and select **Properties**, in the Properties window change the **Nullable** setting to **True**
*This allows us to save a Blog to the database without assigning it a Url*</span></span>
-   <span data-ttu-id="3b1ae-141">習得した手法を使用して、 **PostId** key プロパティを持つ**Post**エンティティを追加します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-141">Using the techniques you just learnt, add a **Post** entity with a **PostId** key property</span></span>
-   <span data-ttu-id="3b1ae-142">**Post**エンティティに**タイトル**と**コンテンツ**のスカラープロパティを追加する</span><span class="sxs-lookup"><span data-stu-id="3b1ae-142">Add **Title** and **Content** scalar properties to the **Post** entity</span></span>

<span data-ttu-id="3b1ae-143">いくつかのエンティティを作成したので、次はそれらの間にアソシエーション (またはリレーションシップ) を追加します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-143">Now that we have a couple of entities, it’s time to add an association (or relationship) between them.</span></span>

-   <span data-ttu-id="3b1ae-144">デザインサーフェイスを右クリックし、追加] **[新規作成]** 、[関連付け の順に選択します。 @no__t</span><span class="sxs-lookup"><span data-stu-id="3b1ae-144">Right-click on the design surface and select **Add New -&gt; Association…**</span></span>
-   <span data-ttu-id="3b1ae-145">リレーションシップの一方の端を、多重度が**1**に設定され、もう一方のエンドポイントが**多数**の @no__t の複数要素の接続性**を持つ** **ブログ**になるようにします。これは、ブログに*多数の投稿があり、投稿が1つのブログに属していることを意味*します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-145">Make one end of the relationship point to **Blog** with a multiplicity of **One** and the other end point to **Post** with a multiplicity of **Many**
    *This means that a Blog has many Posts and a Post belongs to one Blog*</span></span>
-   <span data-ttu-id="3b1ae-146">**[外部キーのプロパティを ' Post ' エンティティに追加する]** チェックボックスがオンになっていることを確認し、 **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-146">Ensure the **Add foreign key properties to 'Post' Entity** box is checked and click **OK**</span></span>

    ![関連付けの追加 (MF)](~/ef6/media/addassociationmf.png)

<span data-ttu-id="3b1ae-148">ここでは、データベースを生成してデータの読み書きに使用できる単純なモデルを用意しました。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-148">We now have a simple model that we can generate a database from and use to read and write data.</span></span>

![モデルの初期](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="3b1ae-150">Visual Studio 2010 の追加手順</span><span class="sxs-lookup"><span data-stu-id="3b1ae-150">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="3b1ae-151">Visual Studio 2010 で作業している場合、Entity Framework の最新バージョンにアップグレードするには、いくつかの追加の手順に従う必要があります。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-151">If you are working in Visual Studio 2010 there are some additional steps you need to follow to upgrade to the latest version of Entity Framework.</span></span> <span data-ttu-id="3b1ae-152">アップグレードは、強化された API サーフェイスにアクセスできるようにするために重要です。これは、より使いやすく、最新のバグ修正にも使用できます。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-152">Upgrading is important because it gives you access to an improved API surface, that is much easier to use, as well as the latest bug fixes.</span></span>

<span data-ttu-id="3b1ae-153">まず、NuGet から最新バージョンの Entity Framework を取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-153">First up, we need to get the latest version of Entity Framework from NuGet.</span></span>

-   <span data-ttu-id="3b1ae-154">**プロジェクト-&gt; NuGet パッケージの管理...** 
     ***[nuget パッケージの管理...]** オプションがない場合は、[最新バージョンの nuget](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)をインストールする必要があり*ます。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-154">**Project –&gt; Manage NuGet Packages…**
*If you don’t have the **Manage NuGet Packages…** option you should install the [latest version of NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)*</span></span>
-   <span data-ttu-id="3b1ae-155">**[オンライン]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-155">Select the **Online** tab</span></span>
-   <span data-ttu-id="3b1ae-156">**Entityframework**パッケージを選択します</span><span class="sxs-lookup"><span data-stu-id="3b1ae-156">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="3b1ae-157">**[インストール]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-157">Click **Install**</span></span>

<span data-ttu-id="3b1ae-158">次に、モデルをスワップして、新しいバージョンの Entity Framework で導入された DbContext API を使用するコードを生成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-158">Next, we need to swap our model to generate code that makes use of the DbContext API, which was introduced in later versions of Entity Framework.</span></span>

-   <span data-ttu-id="3b1ae-159">EF デザイナーでモデルの空の場所を右クリックし、 **[コード生成項目の追加...]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-159">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="3b1ae-160">左側のメニューから **[オンラインテンプレート]** を選択し、 **dbcontext**を検索します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-160">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="3b1ae-161">**C @ no__t の EF 5.X DbContext ジェネレーター**を選択し、名前として「 **b model** 」と入力して、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-161">Select the EF **5.x DbContext Generator for C\#**, enter **BloggingModel** as the name and click **Add**</span></span>

    ![DbContext テンプレート](~/ef6/media/dbcontexttemplate.png)

## <a name="3-generating-the-database"></a><span data-ttu-id="3b1ae-163">3.データベースを生成しています</span><span class="sxs-lookup"><span data-stu-id="3b1ae-163">3. Generating the Database</span></span>

<span data-ttu-id="3b1ae-164">モデルを使用すると、Entity Framework は、モデルを使用してデータの格納と取得を行うことができるデータベーススキーマを計算できます。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-164">Given our model, Entity Framework can calculate a database schema that will allow us to store and retrieve data using the model.</span></span>

<span data-ttu-id="3b1ae-165">Visual Studio と共にインストールされるデータベースサーバーは、インストールされている Visual Studio のバージョンによって異なります。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-165">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="3b1ae-166">Visual Studio 2010 を使用している場合は、SQL Express データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-166">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="3b1ae-167">Visual Studio 2012 を使用している場合は、 [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx)データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-167">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) database.</span></span>

<span data-ttu-id="3b1ae-168">では、データベースを生成してみましょう。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-168">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="3b1ae-169">デザイン画面を右クリックし、 **[モデルからデータベースを生成...]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-169">Right-click on the design surface and select **Generate Database from Model…**</span></span>
-   <span data-ttu-id="3b1ae-170">**[新しい接続]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-170">Click **New Connection…**</span></span> <span data-ttu-id="3b1ae-171">使用している Visual Studio のバージョンに応じて、LocalDB または SQL Express のいずれかを指定し、データベース名として「 **Modelfirst** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-171">and specify either LocalDB or SQL Express, depending on which version of Visual Studio you are using, enter **ModelFirst.Blogging** as the database name.</span></span>

    ![LocalDB 接続の MF](~/ef6/media/localdbconnectionmf.png)

    ![Sql Express 接続の MF](~/ef6/media/sqlexpressconnectionmf.png)

-   <span data-ttu-id="3b1ae-174">[ **OK]** を選択すると、新しいデータベースを作成するかどうかを確認するメッセージが表示されます。 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-174">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="3b1ae-175">**[次へ]** を選択すると、データベーススキーマを作成するスクリプトが Entity Framework Designer によって計算されます。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-175">Select **Next** and the Entity Framework Designer will calculate a script to create the database schema</span></span>
-   <span data-ttu-id="3b1ae-176">スクリプトが表示されたら、 **[完了]** をクリックすると、スクリプトがプロジェクトに追加されて開かれます。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-176">Once the script is displayed, click **Finish** and the script will be added to your project and opened</span></span>
-   <span data-ttu-id="3b1ae-177">スクリプトを右クリックして **[実行]** を選択すると、接続先のデータベースを指定するように求められます。使用している Visual Studio のバージョンに応じて、LocalDB または SQL Server Express を指定します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-177">Right-click on the script and select **Execute**, you will be prompted to specify the database to connect to, specify LocalDB or SQL Server Express, depending on which version of Visual Studio you are using</span></span>

## <a name="4-reading--writing-data"></a><span data-ttu-id="3b1ae-178">4。データの読み取り & 書き込み</span><span class="sxs-lookup"><span data-stu-id="3b1ae-178">4. Reading & Writing Data</span></span>

<span data-ttu-id="3b1ae-179">モデルを作成したので、これを使用していくつかのデータにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-179">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="3b1ae-180">データへのアクセスに使用するクラスは、EDMX ファイルに基づいて自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-180">The classes we are going to use to access data are being automatically generated for you based on the EDMX file.</span></span>

<span data-ttu-id="3b1ae-181">*このスクリーンショットは Visual Studio 2012 からのものです。 Visual Studio 2010 を使用している場合は、BloggingModel.tt ファイルと BloggingModel.Context.tt ファイルは、EDMX ファイルの下に入れ子になっているのではなく、プロジェクトの直下に配置されます。*</span><span class="sxs-lookup"><span data-stu-id="3b1ae-181">*This screen shot is from Visual Studio 2012, if you are using Visual Studio 2010 the BloggingModel.tt and BloggingModel.Context.tt files will be directly under your project rather than nested under the EDMX file.*</span></span>

![生成されたクラス](~/ef6/media/generatedclasses.png)

<span data-ttu-id="3b1ae-183">次に示すように、Program.cs に Main メソッドを実装します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-183">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="3b1ae-184">このコードは、コンテキストの新しいインスタンスを作成し、それを使用して新しいブログを挿入します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-184">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="3b1ae-185">次に、LINQ クエリを使用して、データベースのすべてのブログをタイトル別にアルファベット順に並べ替えて取得します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-185">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

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

<span data-ttu-id="3b1ae-186">これで、アプリケーションを実行してテストできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-186">You can now run the application and test it out.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```

## <a name="5-dealing-with-model-changes"></a><span data-ttu-id="3b1ae-187">5。モデルの変更の処理</span><span class="sxs-lookup"><span data-stu-id="3b1ae-187">5. Dealing with Model Changes</span></span>

<span data-ttu-id="3b1ae-188">次に、モデルに何らかの変更を加えてみましょう。これらの変更を行うときは、データベーススキーマも更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-188">Now it’s time to make some changes to our model, when we make these changes we also need to update the database schema.</span></span>

<span data-ttu-id="3b1ae-189">まず、モデルに新しいユーザーエンティティを追加します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-189">We’ll start by adding a new User entity to our model.</span></span>

-   <span data-ttu-id="3b1ae-190">キーの名前と**文字列**としてユーザー**名**を持つ新しい**ユーザー**エンティティ名を、キーのプロパティの型として追加します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-190">Add a new **User** entity name with **Username** as the key name and **String** as the property type for the key</span></span>

    ![ユーザーエンティティの追加](~/ef6/media/adduserentity.png)

-   <span data-ttu-id="3b1ae-192">デザイン画面で **[ユーザー名]** プロパティを右クリックし、 **[プロパティ**] を選択します。プロパティウィンドウ、 **[MaxLength]** 設定を**50**@no__t に変更します。これにより、*ユーザー名に格納できるデータが50に制限されます。文字*</span><span class="sxs-lookup"><span data-stu-id="3b1ae-192">Right-click on the **Username** property on the design surface and select **Properties**, In the Properties window change the **MaxLength** setting to **50**
    *This restricts the data that can be stored in username to 50 characters*</span></span>
-   <span data-ttu-id="3b1ae-193">**DisplayName**スカラープロパティを**User**エンティティに追加する</span><span class="sxs-lookup"><span data-stu-id="3b1ae-193">Add a **DisplayName** scalar property to the **User** entity</span></span>

<span data-ttu-id="3b1ae-194">これで、モデルが更新され、新しいユーザーエンティティ型に対応するようにデータベースを更新する準備ができました。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-194">We now have an updated model and we are ready to update the database to accommodate our new User entity type.</span></span>

-   <span data-ttu-id="3b1ae-195">デザイン画面を右クリックし、 **[モデルからデータベースを生成]** を選択すると、Entity Framework によって、更新されたモデルに基づいてスキーマを再作成するスクリプトが計算されます。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-195">Right-click on the design surface and select **Generate Database from Model…**, Entity Framework will calculate a script to recreate a schema based on the updated model.</span></span>
-   <span data-ttu-id="3b1ae-196">**[完了]** をクリック</span><span class="sxs-lookup"><span data-stu-id="3b1ae-196">Click **Finish**</span></span>
-   <span data-ttu-id="3b1ae-197">既存の DDL スクリプトとモデルのマッピングおよびストレージ部分の上書きに関する警告が表示される場合は、両方の警告に対して **[はい]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-197">You may receive warnings about overwriting the existing DDL script and the mapping and storage parts of the model, click **Yes** for both these warnings</span></span>
-   <span data-ttu-id="3b1ae-198">データベースを作成するための更新された SQL スクリプトが開きます</span><span class="sxs-lookup"><span data-stu-id="3b1ae-198">The updated SQL script to create the database is opened for you</span></span>  
    <span data-ttu-id="3b1ae-199">*The されるスクリプトは、既存のテーブルをすべて削除してから、スキーマを最初から再作成します。これは、ローカルでの開発では機能しますが、既に配置されているデータベースに変更をプッシュする場合には有効ではありません。既に配置されているデータベースに変更をパブリッシュする必要がある場合は、スクリプトを編集するか、スキーマ比較ツールを使用して移行スクリプトを計算する必要があります。*</span><span class="sxs-lookup"><span data-stu-id="3b1ae-199">*The script that is generated will drop all existing tables and then recreate the schema from scratch. This may work for local development but is not a viable for pushing changes to a database that has already been deployed. If you need to publish changes to a database that has already been deployed, you will need to edit the script or use a schema compare tool to calculate a migration script.*</span></span>
-   <span data-ttu-id="3b1ae-200">スクリプトを右クリックして **[実行]** を選択すると、接続先のデータベースを指定するように求められます。使用している Visual Studio のバージョンに応じて、LocalDB または SQL Server Express を指定します。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-200">Right-click on the script and select **Execute**, you will be prompted to specify the database to connect to, specify LocalDB or SQL Server Express, depending on which version of Visual Studio you are using</span></span>

## <a name="summary"></a><span data-ttu-id="3b1ae-201">まとめ</span><span class="sxs-lookup"><span data-stu-id="3b1ae-201">Summary</span></span>

<span data-ttu-id="3b1ae-202">このチュートリアルでは Model First の開発について説明しました。これにより、EF デザイナーでモデルを作成し、そのモデルからデータベースを生成することができました。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-202">In this walkthrough we looked at Model First development, which allowed us to create a model in the EF Designer and then generate a database from that model.</span></span> <span data-ttu-id="3b1ae-203">次に、モデルを使用して、データベースのデータの読み取りと書き込みを行います。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-203">We then used the model to read and write some data from the database.</span></span> <span data-ttu-id="3b1ae-204">最後に、モデルを更新した後、モデルに一致するようにデータベーススキーマを再作成しました。</span><span class="sxs-lookup"><span data-stu-id="3b1ae-204">Finally, we updated the model and then recreated the database schema to match the model.</span></span>
