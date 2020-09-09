---
title: 自己追跡エンティティのチュートリアル-EF6
description: Entity Framework 6 の自己追跡エンティティのチュートリアル
author: divega
ms.date: 10/23/2016
ms.assetid: b21207c9-1d95-4aa3-ae05-bc5fe300dab0
uid: ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough
ms.openlocfilehash: 942baae158d89acec98e70c391f677349148068c
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616148"
---
# <a name="self-tracking-entities-walkthrough"></a><span data-ttu-id="6620a-103">自己追跡エンティティのチュートリアル</span><span class="sxs-lookup"><span data-stu-id="6620a-103">Self-Tracking Entities Walkthrough</span></span>
> [!IMPORTANT]
> <span data-ttu-id="6620a-104">自己追跡エンティティ テンプレートの使用は現在お勧めしていません。</span><span class="sxs-lookup"><span data-stu-id="6620a-104">We no longer recommend using the self-tracking-entities template.</span></span> <span data-ttu-id="6620a-105">既存のアプリケーションをサポートするためにのみ引き続き使用できます。</span><span class="sxs-lookup"><span data-stu-id="6620a-105">It will only continue to be available to support existing applications.</span></span> <span data-ttu-id="6620a-106">アプリケーションで、エンティティの切断されたグラフを操作する必要がある場合は、代替の方法を検討してください。たとえば、コミュニティによってより積極的に開発された自己追跡エンティティに似たテクノロジである[追跡可能なエンティティ](https://trackableentities.github.io/)を使用するか、または、低レベルの変更追跡 API を使用してカスタム コードを記述してください。</span><span class="sxs-lookup"><span data-stu-id="6620a-106">If your application requires working with disconnected graphs of entities, consider other alternatives such as [Trackable Entities](https://trackableentities.github.io/), which is a technology similar to Self-Tracking-Entities that is more actively developed by the community, or writing custom code using the low-level change tracking APIs.</span></span>

<span data-ttu-id="6620a-107">このチュートリアルでは、Windows Communication Foundation (WCF) サービスが、エンティティグラフを返す操作を公開するシナリオについて説明します。</span><span class="sxs-lookup"><span data-stu-id="6620a-107">This walkthrough demonstrates the scenario in which a Windows Communication Foundation (WCF) service exposes an operation that returns an entity graph.</span></span> <span data-ttu-id="6620a-108">次に、クライアントアプリケーションは、そのグラフを操作し、Entity Framework を使用して更新を検証してデータベースに保存するサービス操作に変更を送信します。</span><span class="sxs-lookup"><span data-stu-id="6620a-108">Next, a client application manipulates that graph and submits the modifications to a service operation that validates and saves the updates to a database using Entity Framework.</span></span>

<span data-ttu-id="6620a-109">このチュートリアルを完了する前に、「 [自己追跡エンティティ](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/index) 」ページを必ずお読みください。</span><span class="sxs-lookup"><span data-stu-id="6620a-109">Before completing this walkthrough make sure you read the [Self-Tracking Entities](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/index) page.</span></span>

<span data-ttu-id="6620a-110">このチュートリアルでは次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="6620a-110">This walkthrough completes the following actions:</span></span>

-   <span data-ttu-id="6620a-111">アクセスするデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="6620a-111">Creates a database to access.</span></span>
-   <span data-ttu-id="6620a-112">モデルを含むクラスライブラリを作成します。</span><span class="sxs-lookup"><span data-stu-id="6620a-112">Creates a class library that contains the model.</span></span>
-   <span data-ttu-id="6620a-113">自己追跡エンティティジェネレーターテンプレートにスワップします。</span><span class="sxs-lookup"><span data-stu-id="6620a-113">Swaps to the Self-Tracking Entity Generator template.</span></span>
-   <span data-ttu-id="6620a-114">エンティティクラスを別のプロジェクトに移動します。</span><span class="sxs-lookup"><span data-stu-id="6620a-114">Moves the entity classes to a separate project.</span></span>
-   <span data-ttu-id="6620a-115">エンティティを照会および保存する操作を公開する WCF サービスを作成します。</span><span class="sxs-lookup"><span data-stu-id="6620a-115">Creates a WCF service that exposes operations to query and save entities.</span></span>
-   <span data-ttu-id="6620a-116">サービスを使用するクライアントアプリケーション (コンソールと WPF) を作成します。</span><span class="sxs-lookup"><span data-stu-id="6620a-116">Creates client applications (Console and WPF) that consume the service.</span></span>

<span data-ttu-id="6620a-117">このチュートリアルでは Database First を使用しますが、同じ手法が Model First にも同様に適用されます。</span><span class="sxs-lookup"><span data-stu-id="6620a-117">We'll use Database First in this walkthrough but the same techniques apply equally to Model First.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="6620a-118">前提条件</span><span class="sxs-lookup"><span data-stu-id="6620a-118">Pre-Requisites</span></span>

<span data-ttu-id="6620a-119">このチュートリアルを完了するには、Visual Studio の最新バージョンが必要です。</span><span class="sxs-lookup"><span data-stu-id="6620a-119">To complete this walkthrough you will need a recent version of Visual Studio.</span></span>

## <a name="create-a-database"></a><span data-ttu-id="6620a-120">データベースの作成</span><span class="sxs-lookup"><span data-stu-id="6620a-120">Create a Database</span></span>

<span data-ttu-id="6620a-121">Visual Studio と共にインストールされるデータベースサーバーは、インストールされている Visual Studio のバージョンによって異なります。</span><span class="sxs-lookup"><span data-stu-id="6620a-121">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="6620a-122">Visual Studio 2012 を使用している場合は、LocalDB データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="6620a-122">If you are using Visual Studio 2012 then you'll be creating a LocalDB database.</span></span>
-   <span data-ttu-id="6620a-123">Visual Studio 2010 を使用している場合は、SQL Express データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="6620a-123">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>

<span data-ttu-id="6620a-124">では、データベースを生成してみましょう。</span><span class="sxs-lookup"><span data-stu-id="6620a-124">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="6620a-125">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="6620a-125">Open Visual Studio</span></span>
-   <span data-ttu-id="6620a-126">**ビュー- &gt; サーバーエクスプローラー**</span><span class="sxs-lookup"><span data-stu-id="6620a-126">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="6620a-127">[**データ接続-接続の &gt; 追加**] を右クリックします。</span><span class="sxs-lookup"><span data-stu-id="6620a-127">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="6620a-128">サーバーエクスプローラーからデータベースに接続していない場合は、データソースとして **Microsoft SQL Server** を選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6620a-128">If you haven’t connected to a database from Server Explorer before you’ll need to select **Microsoft SQL Server** as the data source</span></span>
-   <span data-ttu-id="6620a-129">インストールされているものに応じて、LocalDB または SQL Express に接続します。</span><span class="sxs-lookup"><span data-stu-id="6620a-129">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>
-   <span data-ttu-id="6620a-130">データベース名として「 **STESample** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="6620a-130">Enter **STESample** as the database name</span></span>
-   <span data-ttu-id="6620a-131">[ **OK]** を選択すると、新しいデータベースを作成するかどうかを確認するメッセージが表示されます。 [**はい**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="6620a-131">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="6620a-132">新しいデータベースがに表示されるようになりサーバーエクスプローラー</span><span class="sxs-lookup"><span data-stu-id="6620a-132">The new database will now appear in Server Explorer</span></span>
-   <span data-ttu-id="6620a-133">Visual Studio 2012 を使用している場合</span><span class="sxs-lookup"><span data-stu-id="6620a-133">If you are using Visual Studio 2012</span></span>
    -   <span data-ttu-id="6620a-134">[サーバー エクスプローラー] でこのデータベースを右クリックし、**[新しいクエリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6620a-134">Right-click on the database in Server Explorer and select **New Query**</span></span>
    -   <span data-ttu-id="6620a-135">次の SQL を新しいクエリにコピーし、クエリを右クリックして、[**実行**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="6620a-135">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>
-   <span data-ttu-id="6620a-136">Visual Studio 2010 を使用している場合</span><span class="sxs-lookup"><span data-stu-id="6620a-136">If you are using Visual Studio 2010</span></span>
    -   <span data-ttu-id="6620a-137">[**データの選択]-[ &gt; transact-sql エディター]-[ &gt; 新しいクエリ接続**]</span><span class="sxs-lookup"><span data-stu-id="6620a-137">Select **Data -&gt; Transact SQL Editor -&gt; New Query Connection...**</span></span>
    -   <span data-ttu-id="6620a-138">「」と入力**します。 \\**サーバー名として SQLEXPRESS を指定し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="6620a-138">Enter **.\\SQLEXPRESS** as the server name and click **OK**</span></span>
    -   <span data-ttu-id="6620a-139">クエリエディターの上部にあるドロップダウンから **STESample** データベースを選択します。</span><span class="sxs-lookup"><span data-stu-id="6620a-139">Select the **STESample** database from the drop down at the top of the query editor</span></span>
    -   <span data-ttu-id="6620a-140">次の SQL を新しいクエリにコピーし、クエリを右クリックして、[ **sql の実行**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="6620a-140">Copy the following SQL into the new query, then right-click on the query and select **Execute SQL**</span></span>

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

    SET IDENTITY_INSERT [dbo].[Blogs] ON
    INSERT INTO [dbo].[Blogs] ([BlogId], [Name], [Url]) VALUES (1, N'ADO.NET Blog', N'blogs.msdn.com/adonet')
    SET IDENTITY_INSERT [dbo].[Blogs] OFF
    INSERT INTO [dbo].[Posts] ([Title], [Content], [BlogId]) VALUES (N'Intro to EF', N'Interesting stuff...', 1)
    INSERT INTO [dbo].[Posts] ([Title], [Content], [BlogId]) VALUES (N'What is New', N'More interesting stuff...', 1)
```

## <a name="create-the-model"></a><span data-ttu-id="6620a-141">モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="6620a-141">Create the Model</span></span>

<span data-ttu-id="6620a-142">まず、モデルを配置するためのプロジェクトが必要です。</span><span class="sxs-lookup"><span data-stu-id="6620a-142">First up, we need a project to put the model in.</span></span>

-   <span data-ttu-id="6620a-143">**ファイル- &gt; 新規 &gt; プロジェクト...**</span><span class="sxs-lookup"><span data-stu-id="6620a-143">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="6620a-144">左側のウィンドウで [ **Visual C \# \*\* ] を選択し、[**クラスライブラリ\*\*] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="6620a-144">Select **Visual C\#** from the left pane and then **Class Library**</span></span>
-   <span data-ttu-id="6620a-145">名前として「 **STESample** 」と入力し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="6620a-145">Enter **STESample** as the name and click **OK**</span></span>

<span data-ttu-id="6620a-146">次に、EF デザイナーで単純なモデルを作成して、データベースにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="6620a-146">Now we'll create a simple model in the EF Designer to access our database:</span></span>

-   <span data-ttu-id="6620a-147">**プロジェクト- &gt; 新しい項目の追加...**</span><span class="sxs-lookup"><span data-stu-id="6620a-147">**Project -&gt; Add New Item...**</span></span>
-   <span data-ttu-id="6620a-148">左側のウィンドウから [ **データ** ] を選択し、次に **ADO.NET Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="6620a-148">Select **Data** from the left pane and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="6620a-149">名前として「 **Bのログインモデル**」と入力し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="6620a-149">Enter **BloggingModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="6620a-150">[**データベースから生成**] を選択し、[**次へ**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="6620a-150">Select **Generate from database** and click **Next**</span></span>
-   <span data-ttu-id="6620a-151">前のセクションで作成したデータベースの接続情報を入力します。</span><span class="sxs-lookup"><span data-stu-id="6620a-151">Enter the connection information for the database that you created in the previous section</span></span>
-   <span data-ttu-id="6620a-152">接続文字列の名前として「 **Bのログインコンテキスト**」と入力し、[**次へ**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="6620a-152">Enter **BloggingContext** as the name for the connection string and click **Next**</span></span>
-   <span data-ttu-id="6620a-153">[**テーブル**] の横にあるチェックボックスをオンにし、[**完了**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="6620a-153">Check the box next to **Tables** and click **Finish**</span></span>

## <a name="swap-to-ste-code-generation"></a><span data-ttu-id="6620a-154">貼り付けコード生成にスワップする</span><span class="sxs-lookup"><span data-stu-id="6620a-154">Swap to STE Code Generation</span></span>

<span data-ttu-id="6620a-155">ここで、既定のコード生成を無効にし、自己追跡エンティティにスワップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6620a-155">Now we need to disable the default code generation and swap to Self-Tracking Entities.</span></span>

### <a name="if-you-are-using-visual-studio-2012"></a><span data-ttu-id="6620a-156">Visual Studio 2012 を使用している場合</span><span class="sxs-lookup"><span data-stu-id="6620a-156">If you are using Visual Studio 2012</span></span>

-   <span data-ttu-id="6620a-157">**ソリューションエクスプローラー**で**bBloggingModel.tt**を展開し、 **BloggingModel.tt**と**BloggingModel.Context.tt**を削除します。これに 
     *より、既定のコード生成が無効になります*。</span><span class="sxs-lookup"><span data-stu-id="6620a-157">Expand **BloggingModel.edmx** in **Solution Explorer** and delete the **BloggingModel.tt** and **BloggingModel.Context.tt**
*This will disable the default code generation*</span></span>
-   <span data-ttu-id="6620a-158">EF デザイナー画面で空の領域を右クリックし、[**コード生成項目の追加...** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="6620a-158">Right-click an empty area on the EF Designer surface and select **Add Code Generation Item...**</span></span>
-   <span data-ttu-id="6620a-159">左ペインで [**オンライン**] を選択し、**貼り付け Generator**を検索します。</span><span class="sxs-lookup"><span data-stu-id="6620a-159">Select **Online** from the left pane and search for **STE Generator**</span></span>
-   <span data-ttu-id="6620a-160">\*\* \# C テンプレートの貼り付け Generator**を選択し、名前として「 **stetemplate** 」と入力して、[**追加\*\*] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="6620a-160">Select the **STE Generator for C\#** template, enter **STETemplate** as the name and click **Add**</span></span>
-   <span data-ttu-id="6620a-161">**STETemplate.tt**ファイルと**STETemplate.Context.tt**ファイルは、b ファイルの下に入れ子になっています。</span><span class="sxs-lookup"><span data-stu-id="6620a-161">The **STETemplate.tt** and **STETemplate.Context.tt** files are added nested under the BloggingModel.edmx file</span></span>

### <a name="if-you-are-using-visual-studio-2010"></a><span data-ttu-id="6620a-162">Visual Studio 2010 を使用している場合</span><span class="sxs-lookup"><span data-stu-id="6620a-162">If you are using Visual Studio 2010</span></span>

-   <span data-ttu-id="6620a-163">EF デザイナー画面で空の領域を右クリックし、[**コード生成項目の追加...** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="6620a-163">Right-click an empty area on the EF Designer surface and select **Add Code Generation Item...**</span></span>
-   <span data-ttu-id="6620a-164">左ペインで [**コード**] を選択し、[ **ADO.NET エンティティジェネレーター** ] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="6620a-164">Select **Code** from the left pane and then **ADO.NET Self-Tracking Entity Generator**</span></span>
-   <span data-ttu-id="6620a-165">名前として「 **Stetemplate** 」と入力し、[**追加**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="6620a-165">Enter **STETemplate** as the name and click **Add**</span></span>
-   <span data-ttu-id="6620a-166">**STETemplate.tt**ファイルと**STETemplate.Context.tt**ファイルがプロジェクトに直接追加されます。</span><span class="sxs-lookup"><span data-stu-id="6620a-166">The **STETemplate.tt** and **STETemplate.Context.tt** files are added directly to your project</span></span>

## <a name="move-entity-types-into-separate-project"></a><span data-ttu-id="6620a-167">エンティティ型を別のプロジェクトに移動する</span><span class="sxs-lookup"><span data-stu-id="6620a-167">Move Entity Types into Separate Project</span></span>

<span data-ttu-id="6620a-168">自己追跡エンティティを使用するには、クライアントアプリケーションは、モデルから生成されたエンティティクラスにアクセスする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6620a-168">To use Self-Tracking Entities our client application needs access to the entity classes generated from our model.</span></span> <span data-ttu-id="6620a-169">モデル全体をクライアントアプリケーションに公開する必要がないため、エンティティクラスを別のプロジェクトに移動します。</span><span class="sxs-lookup"><span data-stu-id="6620a-169">Because we don't want to expose the whole model to the client application we're going to move the entity classes into a separate project.</span></span>

<span data-ttu-id="6620a-170">最初の手順では、既存のプロジェクトでのエンティティクラスの生成を停止します。</span><span class="sxs-lookup"><span data-stu-id="6620a-170">The first step is to stop generating entity classes in the existing project:</span></span>

-   <span data-ttu-id="6620a-171">**ソリューションエクスプローラー**の [ **STETemplate.tt** ] を右クリックし、[**プロパティ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="6620a-171">Right-click on **STETemplate.tt** in **Solution Explorer** and select **Properties**</span></span>
-   <span data-ttu-id="6620a-172">[**プロパティ**] ウィンドウで、 **CustomTool**プロパティから**texttemplatingfilegenerator**をクリアします。</span><span class="sxs-lookup"><span data-stu-id="6620a-172">In the **Properties** window clear **TextTemplatingFileGenerator** from the **CustomTool** property</span></span>
-   <span data-ttu-id="6620a-173">**ソリューションエクスプローラー**の [ **STETemplate.tt** ] を展開し、その下に入れ子になっているすべてのファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="6620a-173">Expand **STETemplate.tt** in **Solution Explorer** and delete all files nested under it</span></span>

<span data-ttu-id="6620a-174">次に、新しいプロジェクトを追加し、そこにエンティティクラスを生成します。</span><span class="sxs-lookup"><span data-stu-id="6620a-174">Next, we are going to add a new project and generate the entity classes in it</span></span>

-   <span data-ttu-id="6620a-175">**ファイルの &gt; 追加- &gt; プロジェクト...**</span><span class="sxs-lookup"><span data-stu-id="6620a-175">**File -&gt; Add -&gt; Project...**</span></span>
-   <span data-ttu-id="6620a-176">左側のウィンドウで [ **Visual C \# \*\* ] を選択し、[**クラスライブラリ\*\*] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="6620a-176">Select **Visual C\#** from the left pane and then **Class Library**</span></span>
-   <span data-ttu-id="6620a-177">名前として「STESample」と入力し、[ **OK]** をクリックし**ます。**</span><span class="sxs-lookup"><span data-stu-id="6620a-177">Enter **STESample.Entities** as the name and click **OK**</span></span>
-   <span data-ttu-id="6620a-178">**プロジェクト- &gt; 既存の項目の追加...**</span><span class="sxs-lookup"><span data-stu-id="6620a-178">**Project -&gt; Add Existing Item...**</span></span>
-   <span data-ttu-id="6620a-179">**STESample**プロジェクトフォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="6620a-179">Navigate to the **STESample** project folder</span></span>
-   <span data-ttu-id="6620a-180">すべてのファイルを表示する場合に選択し \*\* \* ます (. \* )\*\*</span><span class="sxs-lookup"><span data-stu-id="6620a-180">Select to view **All Files (\*.\*)**</span></span>
-   <span data-ttu-id="6620a-181">**STETemplate.tt**ファイルを選択します</span><span class="sxs-lookup"><span data-stu-id="6620a-181">Select the **STETemplate.tt** file</span></span>
-   <span data-ttu-id="6620a-182">[**追加**] ボタンの横にあるドロップダウン矢印をクリックし、[**リンクとして追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="6620a-182">Click on the drop down arrow next to the **Add** button and select **Add As Link**</span></span>

    ![リンクテンプレートの追加](~/ef6/media/addlinkedtemplate.png)

<span data-ttu-id="6620a-184">また、エンティティクラスがコンテキストと同じ名前空間で生成されるようにします。</span><span class="sxs-lookup"><span data-stu-id="6620a-184">We're also going to make sure the entity classes get generated in the same namespace as the context.</span></span> <span data-ttu-id="6620a-185">これにより、アプリケーション全体で追加する必要があるステートメントの数が減ります。</span><span class="sxs-lookup"><span data-stu-id="6620a-185">This just reduces the number of using statements we need to add throughout our application.</span></span>

-   <span data-ttu-id="6620a-186">**ソリューションエクスプローラー**のリンクされた**STETemplate.tt**を右クリックし、[**プロパティ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="6620a-186">Right-click on the linked **STETemplate.tt** in **Solution Explorer** and select **Properties**</span></span>
-   <span data-ttu-id="6620a-187">[**プロパティ**] ウィンドウで、[**カスタムツールの名前空間**] を**STESample**に設定します。</span><span class="sxs-lookup"><span data-stu-id="6620a-187">In the **Properties** window set **Custom Tool Namespace** to **STESample**</span></span>

<span data-ttu-id="6620a-188">貼り付けテンプレートによって生成されるコードは、コンパイルする **ために、system.string への** 参照を必要とします。</span><span class="sxs-lookup"><span data-stu-id="6620a-188">The code generated by the STE template will need a reference to **System.Runtime.Serialization** in order to compile.</span></span> <span data-ttu-id="6620a-189">このライブラリは、シリアル化可能なエンティティ型で使用される WCF **DataContract** および **DataMember** 属性に必要です。</span><span class="sxs-lookup"><span data-stu-id="6620a-189">This library is needed for the WCF **DataContract** and **DataMember** attributes that are used on the serializable entity types.</span></span>

-   <span data-ttu-id="6620a-190">**ソリューションエクスプローラー**で**STESample**プロジェクトを右クリックし、[参照の**追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="6620a-190">Right click on the **STESample.Entities** project in **Solution Explorer** and select **Add Reference...**</span></span>
    -   <span data-ttu-id="6620a-191">Visual Studio 2012-[system.string] の横にあるチェックボックスをオンにし、[ **OK** ] をクリックし**ます。**</span><span class="sxs-lookup"><span data-stu-id="6620a-191">In Visual Studio 2012 - check the box next to **System.Runtime.Serialization** and click **OK**</span></span>
    -   <span data-ttu-id="6620a-192">Visual Studio 2010-[system.string] を選択し、[ **OK** ] をクリックし**ます。**</span><span class="sxs-lookup"><span data-stu-id="6620a-192">In Visual Studio 2010 - select **System.Runtime.Serialization** and click **OK**</span></span>

<span data-ttu-id="6620a-193">最後に、コンテキストが含まれているプロジェクトには、エンティティ型への参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="6620a-193">Finally, the project with our context in it will need a reference to the entity types.</span></span>

-   <span data-ttu-id="6620a-194">**ソリューションエクスプローラー**で**STESample**プロジェクトを右クリックし、[**参照の追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="6620a-194">Right click on the **STESample** project in **Solution Explorer** and select **Add Reference...**</span></span>
    -   <span data-ttu-id="6620a-195">Visual Studio 2012-左側のウィンドウから [**ソリューション**] を選択し、STESample の横にあるチェックボックスをオンにして、[ **OK** ] をクリックし**ます。**</span><span class="sxs-lookup"><span data-stu-id="6620a-195">In Visual Studio 2012 - select **Solution** from the left pane, check the box next to **STESample.Entities** and click **OK**</span></span>
    -   <span data-ttu-id="6620a-196">Visual Studio 2010-[**プロジェクト**] タブを選択し、STESample を選択して [ **OK** ] をクリックし**ます。**</span><span class="sxs-lookup"><span data-stu-id="6620a-196">In Visual Studio 2010 - select the **Projects** tab, select **STESample.Entities** and click **OK**</span></span>

>[!NOTE]
> <span data-ttu-id="6620a-197">エンティティ型を別のプロジェクトに移動するもう1つのオプションは、テンプレートファイルを既定の場所からリンクするのではなく、移動することです。</span><span class="sxs-lookup"><span data-stu-id="6620a-197">Another option for moving the entity types to a separate project is to move the template file, rather than linking it from its default location.</span></span> <span data-ttu-id="6620a-198">これを行う場合は、テンプレートの **inputFile** 変数を更新して、.edmx ファイルへの相対パスを指定する必要があります (この例では **... \\Bのモデル .edmx**)。</span><span class="sxs-lookup"><span data-stu-id="6620a-198">If you do this, you will need to update the **inputFile** variable in the template to provide the relative path to the edmx file (in this example that would be **..\\BloggingModel.edmx**).</span></span>

## <a name="create-a-wcf-service"></a><span data-ttu-id="6620a-199">WCF サービスを作成する</span><span class="sxs-lookup"><span data-stu-id="6620a-199">Create a WCF Service</span></span>

<span data-ttu-id="6620a-200">ここで、データを公開する WCF サービスを追加します。まず、プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="6620a-200">Now it's time to add a WCF Service to expose our data, we'll start by creating the project.</span></span>

-   <span data-ttu-id="6620a-201">**ファイルの &gt; 追加- &gt; プロジェクト...**</span><span class="sxs-lookup"><span data-stu-id="6620a-201">**File -&gt; Add -&gt; Project...**</span></span>
-   <span data-ttu-id="6620a-202">左側のウィンドウで [ \*\*Visual C \# \*\* ] を選択し、[ **WCF サービスアプリケーション**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="6620a-202">Select **Visual C\#** from the left pane and then **WCF Service Application**</span></span>
-   <span data-ttu-id="6620a-203">名前として「STESample」と入力し、[ **OK]** をクリックし**ます。**</span><span class="sxs-lookup"><span data-stu-id="6620a-203">Enter **STESample.Service** as the name and click **OK**</span></span>
-   <span data-ttu-id="6620a-204">System.string アセンブリへの参照を追加**します。**</span><span class="sxs-lookup"><span data-stu-id="6620a-204">Add a reference to the **System.Data.Entity** assembly</span></span>
-   <span data-ttu-id="6620a-205">**STESample**プロジェクトおよび**STESample**プロジェクトへの参照を追加する</span><span class="sxs-lookup"><span data-stu-id="6620a-205">Add a reference to the **STESample** and **STESample.Entities** projects</span></span>

<span data-ttu-id="6620a-206">EF 接続文字列をこのプロジェクトにコピーして、実行時に検出されるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6620a-206">We need to copy the EF connection string to this project so that it is found at runtime.</span></span>

-   <span data-ttu-id="6620a-207"> **STESample **プロジェクトの**App.Config**ファイルを開き、 **connectionStrings**要素をコピーします。</span><span class="sxs-lookup"><span data-stu-id="6620a-207">Open the **App.Config** file for the **STESample **project and copy the **connectionStrings** element</span></span>
-   <span data-ttu-id="6620a-208">**ConnectionStrings**要素を、 **Web.Config**ファイルの**configuration**要素の子要素として STESample プロジェクトに貼り付け**ます。**</span><span class="sxs-lookup"><span data-stu-id="6620a-208">Paste the **connectionStrings** element as a child element of the **configuration** element of the **Web.Config** file in the **STESample.Service** project</span></span>

<span data-ttu-id="6620a-209">次に、実際のサービスを実装します。</span><span class="sxs-lookup"><span data-stu-id="6620a-209">Now it's time to implement the actual service.</span></span>

-   <span data-ttu-id="6620a-210">**IService1.cs**を開き、内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6620a-210">Open **IService1.cs** and replace the contents with the following code</span></span>

``` csharp
    using System.Collections.Generic;
    using System.ServiceModel;

    namespace STESample.Service
    {
        [ServiceContract]
        public interface IService1
        {
            [OperationContract]
            List<Blog> GetBlogs();

            [OperationContract]
            void UpdateBlog(Blog blog);
        }
    }
```

-   <span data-ttu-id="6620a-211">Service1 を開き、内容を次のコードに置き換え**ます。**</span><span class="sxs-lookup"><span data-stu-id="6620a-211">Open **Service1.svc** and replace the contents with the following code</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data;
    using System.Linq;

    namespace STESample.Service
    {
        public class Service1 : IService1
        {
            /// <summary>
            /// Gets all the Blogs and related Posts.
            /// </summary>
            public List<Blog> GetBlogs()
            {
                using (BloggingContext context = new BloggingContext())
                {
                    return context.Blogs.Include("Posts").ToList();
                }
            }

            /// <summary>
            /// Updates Blog and its related Posts.
            /// </summary>
            public void UpdateBlog(Blog blog)
            {
                using (BloggingContext context = new BloggingContext())
                {
                    try
                    {
                        // TODO: Perform validation on the updated order before applying the changes.

                        // The ApplyChanges method examines the change tracking information
                        // contained in the graph of self-tracking entities to infer the set of operations
                        // that need to be performed to reflect the changes in the database.
                        context.Blogs.ApplyChanges(blog);
                        context.SaveChanges();

                    }
                    catch (UpdateException)
                    {
                        // To avoid propagating exception messages that contain sensitive data to the client tier
                        // calls to ApplyChanges and SaveChanges should be wrapped in exception handling code.
                        throw new InvalidOperationException("Failed to update. Try your request again.");
                    }
                }
            }        
        }
    }
```

## <a name="consume-the-service-from-a-console-application"></a><span data-ttu-id="6620a-212">コンソールアプリケーションからサービスを使用する</span><span class="sxs-lookup"><span data-stu-id="6620a-212">Consume the Service from a Console Application</span></span>

<span data-ttu-id="6620a-213">ここでは、サービスを使用するコンソールアプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="6620a-213">Let's create a console application that uses our service.</span></span>

-   <span data-ttu-id="6620a-214">**ファイル- &gt; 新規 &gt; プロジェクト...**</span><span class="sxs-lookup"><span data-stu-id="6620a-214">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="6620a-215">左側のウィンドウで [ **Visual C \# \*\* ] を選択し、[**コンソールアプリケーション\*\*] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="6620a-215">Select **Visual C\#** from the left pane and then **Console Application**</span></span>
-   <span data-ttu-id="6620a-216">名前として「STESample」と入力し、[ **OK]** をクリックし**ます。**</span><span class="sxs-lookup"><span data-stu-id="6620a-216">Enter **STESample.ConsoleTest** as the name and click **OK**</span></span>
-   <span data-ttu-id="6620a-217">**STESample**プロジェクトへの参照を追加する</span><span class="sxs-lookup"><span data-stu-id="6620a-217">Add a reference to the **STESample.Entities** project</span></span>

<span data-ttu-id="6620a-218">WCF サービスへのサービス参照が必要です</span><span class="sxs-lookup"><span data-stu-id="6620a-218">We need a service reference to our WCF service</span></span>

-   <span data-ttu-id="6620a-219">**ソリューションエクスプローラー**で [ **STESample] テスト**プロジェクトを右クリックし、[**サービス参照の追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="6620a-219">Right-click the **STESample.ConsoleTest** project in **Solution Explorer** and select **Add Service Reference...**</span></span>
-   <span data-ttu-id="6620a-220">[**検出**] をクリック</span><span class="sxs-lookup"><span data-stu-id="6620a-220">Click **Discover**</span></span>
-   <span data-ttu-id="6620a-221">名前空間として「 **B、Service** 」と入力し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="6620a-221">Enter **BloggingService** as the namespace and click **OK**</span></span>

<span data-ttu-id="6620a-222">これで、サービスを使用するコードを記述できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="6620a-222">Now we can write some code to consume the service.</span></span>

-   <span data-ttu-id="6620a-223">**Program.cs**を開き、内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6620a-223">Open **Program.cs** and replace the contents with the following code.</span></span>

``` csharp
    using STESample.ConsoleTest.BloggingService;
    using System;
    using System.Linq;

    namespace STESample.ConsoleTest
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Print out the data before we change anything
                Console.WriteLine("Initial Data:");
                DisplayBlogsAndPosts();

                // Add a new Blog and some Posts
                AddBlogAndPost();
                Console.WriteLine("After Adding:");
                DisplayBlogsAndPosts();

                // Modify the Blog and one of its Posts
                UpdateBlogAndPost();
                Console.WriteLine("After Update:");
                DisplayBlogsAndPosts();

                // Delete the Blog and its Posts
                DeleteBlogAndPost();
                Console.WriteLine("After Delete:");
                DisplayBlogsAndPosts();

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            static void DisplayBlogsAndPosts()
            {
                using (var service = new Service1Client())
                {
                    // Get all Blogs (and Posts) from the service
                    // and print them to the console
                    var blogs = service.GetBlogs();
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(blog.Name);
                        foreach (var post in blog.Posts)
                        {
                            Console.WriteLine(" - {0}", post.Title);
                        }
                    }
                }

                Console.WriteLine();
                Console.WriteLine();
            }

            static void AddBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Create a new Blog with a couple of Posts
                    var newBlog = new Blog
                    {
                        Name = "The New Blog",
                        Posts =
                        {
                            new Post { Title = "Welcome to the new blog"},
                            new Post { Title = "What's new on the new blog"}
                        }
                    };

                    // Save the changes using the service
                    service.UpdateBlog(newBlog);
                }
            }

            static void UpdateBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Get all the Blogs
                    var blogs = service.GetBlogs();

                    // Use LINQ to Objects to find The New Blog
                    var blog = blogs.First(b => b.Name == "The New Blog");

                    // Update the Blogs name
                    blog.Name = "The Not-So-New Blog";

                    // Update one of the related posts
                    blog.Posts.First().Content = "Some interesting content...";

                    // Save the changes using the service
                    service.UpdateBlog(blog);
                }
            }

            static void DeleteBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Get all the Blogs
                    var blogs = service.GetBlogs();

                    // Use LINQ to Objects to find The Not-So-New Blog
                    var blog = blogs.First(b => b.Name == "The Not-So-New Blog");

                    // Mark all related Posts for deletion
                    // We need to call ToList because each Post will be removed from the
                    // Posts collection when we call MarkAsDeleted
                    foreach (var post in blog.Posts.ToList())
                    {
                        post.MarkAsDeleted();
                    }

                    // Mark the Blog for deletion
                    blog.MarkAsDeleted();

                    // Save the changes using the service
                    service.UpdateBlog(blog);
                }
            }
        }
    }
```

<span data-ttu-id="6620a-224">アプリケーションを実行して動作を確認できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="6620a-224">You can now run the application to see it in action.</span></span>

-   <span data-ttu-id="6620a-225">**ソリューションエクスプローラー**で STESample テストプロジェクトを右クリックし、[デバッグ] **、[ &gt; 新しいインスタンスを開始**] の順に選択し**ます。**</span><span class="sxs-lookup"><span data-stu-id="6620a-225">Right-click the **STESample.ConsoleTest** project in **Solution Explorer** and select **Debug -&gt; Start new instance**</span></span>

<span data-ttu-id="6620a-226">アプリケーションの実行時に、次の出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6620a-226">You'll see the following output when the application executes.</span></span>

```console
Initial Data:
ADO.NET Blog
- Intro to EF
- What is New

After Adding:
ADO.NET Blog
- Intro to EF
- What is New
The New Blog
- Welcome to the new blog
- What's new on the new blog

After Update:
ADO.NET Blog
- Intro to EF
- What is New
The Not-So-New Blog
- Welcome to the new blog
- What's new on the new blog

After Delete:
ADO.NET Blog
- Intro to EF
- What is New

Press any key to exit...
```

## <a name="consume-the-service-from-a-wpf-application"></a><span data-ttu-id="6620a-227">WPF アプリケーションからサービスを使用する</span><span class="sxs-lookup"><span data-stu-id="6620a-227">Consume the Service from a WPF Application</span></span>

<span data-ttu-id="6620a-228">ここでは、サービスを使用する WPF アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="6620a-228">Let's create a WPF application that uses our service.</span></span>

-   <span data-ttu-id="6620a-229">**ファイル- &gt; 新規 &gt; プロジェクト...**</span><span class="sxs-lookup"><span data-stu-id="6620a-229">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="6620a-230">左側のウィンドウで [ \*\*Visual C \# \*\* ] を選択し、[ **WPF アプリケーション**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="6620a-230">Select **Visual C\#** from the left pane and then **WPF Application**</span></span>
-   <span data-ttu-id="6620a-231">名前として「 **STESample. WPFTest** 」と入力し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="6620a-231">Enter **STESample.WPFTest** as the name and click **OK**</span></span>
-   <span data-ttu-id="6620a-232">**STESample**プロジェクトへの参照を追加する</span><span class="sxs-lookup"><span data-stu-id="6620a-232">Add a reference to the **STESample.Entities** project</span></span>

<span data-ttu-id="6620a-233">WCF サービスへのサービス参照が必要です</span><span class="sxs-lookup"><span data-stu-id="6620a-233">We need a service reference to our WCF service</span></span>

-   <span data-ttu-id="6620a-234">**ソリューションエクスプローラー**で**STESample**プロジェクトを右クリックし、[**サービス参照の追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="6620a-234">Right-click the **STESample.WPFTest** project in **Solution Explorer** and select **Add Service Reference...**</span></span>
-   <span data-ttu-id="6620a-235">[**検出**] をクリック</span><span class="sxs-lookup"><span data-stu-id="6620a-235">Click **Discover**</span></span>
-   <span data-ttu-id="6620a-236">名前空間として「 **B、Service** 」と入力し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="6620a-236">Enter **BloggingService** as the namespace and click **OK**</span></span>

<span data-ttu-id="6620a-237">これで、サービスを使用するコードを記述できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="6620a-237">Now we can write some code to consume the service.</span></span>

-   <span data-ttu-id="6620a-238">**Mainwindow.xaml**を開き、内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6620a-238">Open **MainWindow.xaml** and replace the contents with the following code.</span></span>

``` xaml
    <Window
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:STESample="clr-namespace:STESample;assembly=STESample.Entities"
        mc:Ignorable="d" x:Class="STESample.WPFTest.MainWindow"
        Title="MainWindow" Height="350" Width="525" Loaded="Window_Loaded">

        <Window.Resources>
            <CollectionViewSource
                x:Key="blogViewSource"
                d:DesignSource="{d:DesignInstance {x:Type STESample:Blog}, CreateList=True}"/>
            <CollectionViewSource
                x:Key="blogPostsViewSource"
                Source="{Binding Posts, Source={StaticResource blogViewSource}}"/>
        </Window.Resources>

        <Grid DataContext="{StaticResource blogViewSource}">
            <DataGrid AutoGenerateColumns="False" EnableRowVirtualization="True"
                      ItemsSource="{Binding}" Margin="10,10,10,179">
                <DataGrid.Columns>
                    <DataGridTextColumn Binding="{Binding BlogId}" Header="Id" Width="Auto" IsReadOnly="True" />
                    <DataGridTextColumn Binding="{Binding Name}" Header="Name" Width="Auto"/>
                    <DataGridTextColumn Binding="{Binding Url}" Header="Url" Width="Auto"/>
                </DataGrid.Columns>
            </DataGrid>
            <DataGrid AutoGenerateColumns="False" EnableRowVirtualization="True"
                      ItemsSource="{Binding Source={StaticResource blogPostsViewSource}}" Margin="10,145,10,38">
                <DataGrid.Columns>
                    <DataGridTextColumn Binding="{Binding PostId}" Header="Id" Width="Auto"  IsReadOnly="True"/>
                    <DataGridTextColumn Binding="{Binding Title}" Header="Title" Width="Auto"/>
                    <DataGridTextColumn Binding="{Binding Content}" Header="Content" Width="Auto"/>
                </DataGrid.Columns>
            </DataGrid>
            <Button Width="68" Height="23" HorizontalAlignment="Right" VerticalAlignment="Bottom"
                    Margin="0,0,10,10" Click="buttonSave_Click">Save</Button>
        </Grid>
    </Window>
```

-   <span data-ttu-id="6620a-239">Mainwindow.xaml (**MainWindow.xaml.cs**) の分離コードを開き、内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6620a-239">Open the code behind for MainWindow (**MainWindow.xaml.cs**) and replace the contents with the following code</span></span>

``` csharp
    using STESample.WPFTest.BloggingService;
    using System.Collections.Generic;
    using System.Linq;
    using System.Windows;
    using System.Windows.Data;

    namespace STESample.WPFTest
    {
        public partial class MainWindow : Window
        {
            public MainWindow()
            {
                InitializeComponent();
            }

            private void Window_Loaded(object sender, RoutedEventArgs e)
            {
                using (var service = new Service1Client())
                {
                    // Find the view source for Blogs and populate it with all Blogs (and related Posts)
                    // from the Service. The default editing functionality of WPF will allow the objects
                    // to be manipulated on the screen.
                    var blogsViewSource = (CollectionViewSource)this.FindResource("blogViewSource");
                    blogsViewSource.Source = service.GetBlogs().ToList();
                }
            }

            private void buttonSave_Click(object sender, RoutedEventArgs e)
            {
                using (var service = new Service1Client())
                {
                    // Get the blogs that are bound to the screen
                    var blogsViewSource = (CollectionViewSource)this.FindResource("blogViewSource");
                    var blogs = (List<Blog>)blogsViewSource.Source;

                    // Save all Blogs and related Posts
                    foreach (var blog in blogs)
                    {
                        service.UpdateBlog(blog);
                    }

                    // Re-query for data to get database-generated keys etc.
                    blogsViewSource.Source = service.GetBlogs().ToList();
                }
            }
        }
    }
```

<span data-ttu-id="6620a-240">アプリケーションを実行して動作を確認できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="6620a-240">You can now run the application to see it in action.</span></span>

-   <span data-ttu-id="6620a-241">**ソリューションエクスプローラー**で STESample プロジェクトを右クリックし、[デバッグ] **、[ &gt; 新しいインスタンスを開始**] の順に選択し**ます。**</span><span class="sxs-lookup"><span data-stu-id="6620a-241">Right-click the **STESample.WPFTest** project in **Solution Explorer** and select **Debug -&gt; Start new instance**</span></span>
-   <span data-ttu-id="6620a-242">画面を使用してデータを操作し、[ **保存** ] ボタンを使用してサービスで保存することができます。</span><span class="sxs-lookup"><span data-stu-id="6620a-242">You can manipulate the data using the screen and save it via the service using the **Save** button</span></span>

![WPF メインウィンドウ](~/ef6/media/wpf.png)
