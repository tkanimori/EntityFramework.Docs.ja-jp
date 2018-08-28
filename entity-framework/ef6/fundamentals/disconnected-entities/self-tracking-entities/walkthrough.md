---
title: 自己追跡エンティティのチュートリアル - EF6
author: divega
ms.date: 2016-10-23
ms.assetid: b21207c9-1d95-4aa3-ae05-bc5fe300dab0
ms.openlocfilehash: 64ca9ae42df1a1c740131e254b8f80f67b2f9f97
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995422"
---
# <a name="self-tracking-entities-walkthrough"></a><span data-ttu-id="ccad2-102">自己追跡エンティティのチュートリアル</span><span class="sxs-lookup"><span data-stu-id="ccad2-102">Self-Tracking Entities Walkthrough</span></span>
> [!IMPORTANT]
> <span data-ttu-id="ccad2-103">自己追跡エンティティ テンプレートの使用は現在お勧めしていません。</span><span class="sxs-lookup"><span data-stu-id="ccad2-103">We no longer recommend using the self-tracking-entities template.</span></span> <span data-ttu-id="ccad2-104">既存のアプリケーションをサポートするためにのみ引き続き使用できます。</span><span class="sxs-lookup"><span data-stu-id="ccad2-104">It will only continue to be available to support existing applications.</span></span> <span data-ttu-id="ccad2-105">アプリケーションで、エンティティの切断されたグラフを操作する必要がある場合は、代替の方法を検討してください。たとえば、コミュニティによってより積極的に開発された自己追跡エンティティに似たテクノロジである[追跡可能なエンティティ](http://trackableentities.github.io/)を使用するか、または、低レベルの変更追跡 API を使用してカスタム コードを記述してください。</span><span class="sxs-lookup"><span data-stu-id="ccad2-105">If your application requires working with disconnected graphs of entities, consider other alternatives such as [Trackable Entities](http://trackableentities.github.io/), which is a technology similar to Self-Tracking-Entities that is more actively developed by the community, or writing custom code using the low-level change tracking APIs.</span></span>

<span data-ttu-id="ccad2-106">このチュートリアルでは、Windows Communication Foundation (WCF) サービスをエンティティ グラフを返す操作を公開するシナリオを示します。</span><span class="sxs-lookup"><span data-stu-id="ccad2-106">This walkthrough demonstrates the scenario in which a Windows Communication Foundation (WCF) service exposes an operation that returns an entity graph.</span></span> <span data-ttu-id="ccad2-107">次に、クライアント アプリケーションでは、そのグラフを操作し、検証および Entity Framework を使用してデータベースに、更新プログラムを保存するサービス操作への変更を送信します。</span><span class="sxs-lookup"><span data-stu-id="ccad2-107">Next, a client application manipulates that graph and submits the modifications to a service operation that validates and saves the updates to a database using Entity Framework.</span></span>

<span data-ttu-id="ccad2-108">このチュートリアルを完了する前に確認してください、[自己追跡エンティティ](index.md)ページ。</span><span class="sxs-lookup"><span data-stu-id="ccad2-108">Before completing this walkthrough make sure you read the [Self-Tracking Entities](index.md) page.</span></span>

<span data-ttu-id="ccad2-109">このチュートリアルでは次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="ccad2-109">This walkthrough completes the following actions:</span></span>

-   <span data-ttu-id="ccad2-110">アクセスするデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="ccad2-110">Creates a database to access.</span></span>
-   <span data-ttu-id="ccad2-111">モデルを含むクラス ライブラリを作成します。</span><span class="sxs-lookup"><span data-stu-id="ccad2-111">Creates a class library that contains the model.</span></span>
-   <span data-ttu-id="ccad2-112">Self-Tracking Entity Generator テンプレートをスワップします。</span><span class="sxs-lookup"><span data-stu-id="ccad2-112">Swaps to the Self-Tracking Entity Generator template.</span></span>
-   <span data-ttu-id="ccad2-113">エンティティ クラスを別のプロジェクトに移動します。</span><span class="sxs-lookup"><span data-stu-id="ccad2-113">Moves the entity classes to a separate project.</span></span>
-   <span data-ttu-id="ccad2-114">クエリを実行し、エンティティを保存する操作を公開する WCF サービスを作成します。</span><span class="sxs-lookup"><span data-stu-id="ccad2-114">Creates a WCF service that exposes operations to query and save entities.</span></span>
-   <span data-ttu-id="ccad2-115">サービスを使用するアプリケーション (コンソールおよび WPF) クライアントを作成します。</span><span class="sxs-lookup"><span data-stu-id="ccad2-115">Creates client applications (Console and WPF) that consume the service.</span></span>

<span data-ttu-id="ccad2-116">このチュートリアルでは Database First 使用しますが、同じ手法は、モデルを最初に等しく適用されます。</span><span class="sxs-lookup"><span data-stu-id="ccad2-116">We'll use Database First in this walkthrough but the same techniques apply equally to Model First.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="ccad2-117">前提条件</span><span class="sxs-lookup"><span data-stu-id="ccad2-117">Pre-Requisites</span></span>

<span data-ttu-id="ccad2-118">このチュートリアルを完了するには、Visual Studio の最新バージョンを必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccad2-118">To complete this walkthrough you will need a recent version of Visual Studio.</span></span>

## <a name="create-a-database"></a><span data-ttu-id="ccad2-119">データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="ccad2-119">Create a Database</span></span>

<span data-ttu-id="ccad2-120">Visual Studio と共にインストールされているデータベース サーバーは、インストールした Visual Studio のバージョンによって異なります。</span><span class="sxs-lookup"><span data-stu-id="ccad2-120">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="ccad2-121">Visual Studio 2012 を使用している場合、作成する LocalDB データベース。</span><span class="sxs-lookup"><span data-stu-id="ccad2-121">If you are using Visual Studio 2012 then you'll be creating a LocalDB database.</span></span>
-   <span data-ttu-id="ccad2-122">Visual Studio 2010 を使用している場合は、SQL Express データベースを作成するがします。</span><span class="sxs-lookup"><span data-stu-id="ccad2-122">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>

<span data-ttu-id="ccad2-123">データベースを生成してみましょう。</span><span class="sxs-lookup"><span data-stu-id="ccad2-123">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="ccad2-124">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="ccad2-124">Open Visual Studio</span></span>
-   <span data-ttu-id="ccad2-125">**ビュー -&gt;サーバー エクスプ ローラー**</span><span class="sxs-lookup"><span data-stu-id="ccad2-125">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="ccad2-126">右クリックして**データ接続 -&gt;接続の追加.**</span><span class="sxs-lookup"><span data-stu-id="ccad2-126">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="ccad2-127">まだデータベースに接続して、サーバー エクスプ ローラーから選択する必要があります前に場合**Microsoft SQL Server**データ ソースとして</span><span class="sxs-lookup"><span data-stu-id="ccad2-127">If you haven’t connected to a database from Server Explorer before you’ll need to select **Microsoft SQL Server** as the data source</span></span>
-   <span data-ttu-id="ccad2-128">LocalDB または SQL Express をインストールしたものによってのいずれかに接続します。</span><span class="sxs-lookup"><span data-stu-id="ccad2-128">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>
-   <span data-ttu-id="ccad2-129">入力**STESample**データベース名として</span><span class="sxs-lookup"><span data-stu-id="ccad2-129">Enter **STESample** as the database name</span></span>
-   <span data-ttu-id="ccad2-130">選択**OK**かどうかは、新しいデータベースを作成するように要求がある **[はい]**</span><span class="sxs-lookup"><span data-stu-id="ccad2-130">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="ccad2-131">新しいデータベースがサーバー エクスプ ローラーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="ccad2-131">The new database will now appear in Server Explorer</span></span>
-   <span data-ttu-id="ccad2-132">Visual Studio 2012 を使用している場合</span><span class="sxs-lookup"><span data-stu-id="ccad2-132">If you are using Visual Studio 2012</span></span>
    -   <span data-ttu-id="ccad2-133">サーバー エクスプ ローラーでデータベースを右クリックし、選択**新しいクエリ**</span><span class="sxs-lookup"><span data-stu-id="ccad2-133">Right-click on the database in Server Explorer and select **New Query**</span></span>
    -   <span data-ttu-id="ccad2-134">新しいクエリに、次の SQL をコピーし、クエリを選択します右クリックし、 **Execute**</span><span class="sxs-lookup"><span data-stu-id="ccad2-134">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>
-   <span data-ttu-id="ccad2-135">Visual Studio 2010 を使用している場合</span><span class="sxs-lookup"><span data-stu-id="ccad2-135">If you are using Visual Studio 2010</span></span>
    -   <span data-ttu-id="ccad2-136">選択**データ -&gt; Transact SQL エディター -&gt;新しいクエリ接続しています.**</span><span class="sxs-lookup"><span data-stu-id="ccad2-136">Select **Data -&gt; Transact SQL Editor -&gt; New Query Connection...**</span></span>
    -   <span data-ttu-id="ccad2-137">入力 **.\\SQLEXPRESS**サーバー名をクリックします**OK**</span><span class="sxs-lookup"><span data-stu-id="ccad2-137">Enter **.\\SQLEXPRESS** as the server name and click **OK**</span></span>
    -   <span data-ttu-id="ccad2-138">選択、 **STESample**クエリ エディターの上部にある下のドロップダウンからデータベース</span><span class="sxs-lookup"><span data-stu-id="ccad2-138">Select the **STESample** database from the drop down at the top of the query editor</span></span>
    -   <span data-ttu-id="ccad2-139">新しいクエリに、次の SQL をコピーし、クエリを選択します右クリックし、 **SQL の実行**</span><span class="sxs-lookup"><span data-stu-id="ccad2-139">Copy the following SQL into the new query, then right-click on the query and select **Execute SQL**</span></span>

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

## <a name="create-the-model"></a><span data-ttu-id="ccad2-140">モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="ccad2-140">Create the Model</span></span>

<span data-ttu-id="ccad2-141">Up、まず最初に、モデルを配置するプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="ccad2-141">First up, we need a project to put the model in.</span></span>

-   <span data-ttu-id="ccad2-142">**ファイル -&gt;新機能 -&gt;プロジェクト.**</span><span class="sxs-lookup"><span data-stu-id="ccad2-142">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="ccad2-143">選択**Visual C\#** 左側のウィンドウからし**クラス ライブラリ**</span><span class="sxs-lookup"><span data-stu-id="ccad2-143">Select **Visual C\#** from the left pane and then **Class Library**</span></span>
-   <span data-ttu-id="ccad2-144">入力**STESample**名をクリックします**OK**</span><span class="sxs-lookup"><span data-stu-id="ccad2-144">Enter **STESample** as the name and click **OK**</span></span>

<span data-ttu-id="ccad2-145">データベースへのアクセスに EF Designer で単純なモデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="ccad2-145">Now we'll create a simple model in the EF Designer to access our database:</span></span>

-   <span data-ttu-id="ccad2-146">**プロジェクト -&gt;新しい項目を追加しています.**</span><span class="sxs-lookup"><span data-stu-id="ccad2-146">**Project -&gt; Add New Item...**</span></span>
-   <span data-ttu-id="ccad2-147">選択**データ**左側のウィンドウからし**ADO.NET Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="ccad2-147">Select **Data** from the left pane and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="ccad2-148">入力**BloggingModel**名をクリックします**OK**</span><span class="sxs-lookup"><span data-stu-id="ccad2-148">Enter **BloggingModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="ccad2-149">選択**データベースから生成**クリック **[次へ]**</span><span class="sxs-lookup"><span data-stu-id="ccad2-149">Select **Generate from database** and click **Next**</span></span>
-   <span data-ttu-id="ccad2-150">前のセクションで作成したデータベースの接続情報を入力します。</span><span class="sxs-lookup"><span data-stu-id="ccad2-150">Enter the connection information for the database that you created in the previous section</span></span>
-   <span data-ttu-id="ccad2-151">入力**BloggingContext**し接続文字列の名前として **[次へ]**</span><span class="sxs-lookup"><span data-stu-id="ccad2-151">Enter **BloggingContext** as the name for the connection string and click **Next**</span></span>
-   <span data-ttu-id="ccad2-152">次に、ボックスをオン**テーブル**クリック **[完了]**</span><span class="sxs-lookup"><span data-stu-id="ccad2-152">Check the box next to **Tables** and click **Finish**</span></span>

## <a name="swap-to-ste-code-generation"></a><span data-ttu-id="ccad2-153">コード生成の STE に切り替えます</span><span class="sxs-lookup"><span data-stu-id="ccad2-153">Swap to STE Code Generation</span></span>

<span data-ttu-id="ccad2-154">既定のコード生成とスワップ自己追跡エンティティを無効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccad2-154">Now we need to disable the default code generation and swap to Self-Tracking Entities.</span></span>

### <a name="if-you-are-using-visual-studio-2012"></a><span data-ttu-id="ccad2-155">Visual Studio 2012 を使用している場合</span><span class="sxs-lookup"><span data-stu-id="ccad2-155">If you are using Visual Studio 2012</span></span>

-   <span data-ttu-id="ccad2-156">展開**BloggingModel.edmx**で**ソリューション エクスプ ローラー**を削除し、 **BloggingModel.tt**と**BloggingModel.Context.tt** 
    *これが既定のコード生成を無効になります*</span><span class="sxs-lookup"><span data-stu-id="ccad2-156">Expand **BloggingModel.edmx** in **Solution Explorer** and delete the **BloggingModel.tt** and **BloggingModel.Context.tt**
*This will disable the default code generation*</span></span>
-   <span data-ttu-id="ccad2-157">EF デザイナー サーフェスと選択の空の領域を右クリックして**コード生成項目の追加.**</span><span class="sxs-lookup"><span data-stu-id="ccad2-157">Right-click an empty area on the EF Designer surface and select **Add Code Generation Item...**</span></span>
-   <span data-ttu-id="ccad2-158">選択**オンライン**、左側のウィンドウの検索から**STE ジェネレーター**</span><span class="sxs-lookup"><span data-stu-id="ccad2-158">Select **Online** from the left pane and search for **STE Generator**</span></span>
-   <span data-ttu-id="ccad2-159">選択、 **C の STE ジェネレーター\#** テンプレート入力**STETemplate**名をクリックします**追加**</span><span class="sxs-lookup"><span data-stu-id="ccad2-159">Select the **STE Generator for C\#** template, enter **STETemplate** as the name and click **Add**</span></span>
-   <span data-ttu-id="ccad2-160">**STETemplate.tt**と**STETemplate.Context.tt** BloggingModel.edmx ファイルの下で入れ子になったファイルが追加されます</span><span class="sxs-lookup"><span data-stu-id="ccad2-160">The **STETemplate.tt** and **STETemplate.Context.tt** files are added nested under the BloggingModel.edmx file</span></span>

### <a name="if-you-are-using-visual-studio-2010"></a><span data-ttu-id="ccad2-161">Visual Studio 2010 を使用している場合</span><span class="sxs-lookup"><span data-stu-id="ccad2-161">If you are using Visual Studio 2010</span></span>

-   <span data-ttu-id="ccad2-162">EF デザイナー サーフェスと選択の空の領域を右クリックして**コード生成項目の追加.**</span><span class="sxs-lookup"><span data-stu-id="ccad2-162">Right-click an empty area on the EF Designer surface and select **Add Code Generation Item...**</span></span>
-   <span data-ttu-id="ccad2-163">選択**コード**左側のウィンドウからし**ADO.NET Self-Tracking Entity Generator**</span><span class="sxs-lookup"><span data-stu-id="ccad2-163">Select **Code** from the left pane and then **ADO.NET Self-Tracking Entity Generator**</span></span>
-   <span data-ttu-id="ccad2-164">入力**STETemplate**名をクリックします**追加**</span><span class="sxs-lookup"><span data-stu-id="ccad2-164">Enter **STETemplate** as the name and click **Add**</span></span>
-   <span data-ttu-id="ccad2-165">**STETemplate.tt**と**STETemplate.Context.tt**ファイルがプロジェクトに直接追加されます</span><span class="sxs-lookup"><span data-stu-id="ccad2-165">The **STETemplate.tt** and **STETemplate.Context.tt** files are added directly to your project</span></span>

## <a name="move-entity-types-into-separate-project"></a><span data-ttu-id="ccad2-166">エンティティ型を別のプロジェクトに移動します。</span><span class="sxs-lookup"><span data-stu-id="ccad2-166">Move Entity Types into Separate Project</span></span>

<span data-ttu-id="ccad2-167">自己追跡エンティティを使用して、クライアント アプリケーションには、モデルから生成されたエンティティ クラスへのアクセスが必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccad2-167">To use Self-Tracking Entities our client application needs access to the entity classes generated from our model.</span></span> <span data-ttu-id="ccad2-168">クライアント アプリケーションにモデル全体を公開するたくないので、エンティティ クラスを別のプロジェクトに移動いきます。</span><span class="sxs-lookup"><span data-stu-id="ccad2-168">Because we don't want to expose the whole model to the client application we're going to move the entity classes into a separate project.</span></span>

<span data-ttu-id="ccad2-169">最初の手順では、既存のプロジェクトでエンティティ クラスの生成を停止します。</span><span class="sxs-lookup"><span data-stu-id="ccad2-169">The first step is to stop generating entity classes in the existing project:</span></span>

-   <span data-ttu-id="ccad2-170">右クリックして**STETemplate.tt**で**ソリューション エクスプ ローラー**選択**プロパティ**</span><span class="sxs-lookup"><span data-stu-id="ccad2-170">Right-click on **STETemplate.tt** in **Solution Explorer** and select **Properties**</span></span>
-   <span data-ttu-id="ccad2-171">**プロパティ**ウィンドウのクリア**TextTemplatingFileGenerator**から、 **CustomTool**プロパティ</span><span class="sxs-lookup"><span data-stu-id="ccad2-171">In the **Properties** window clear **TextTemplatingFileGenerator** from the **CustomTool** property</span></span>
-   <span data-ttu-id="ccad2-172">展開**STETemplate.tt**で**ソリューション エクスプ ローラー**およびその下にある入れ子になったすべてのファイルの削除</span><span class="sxs-lookup"><span data-stu-id="ccad2-172">Expand **STETemplate.tt** in **Solution Explorer** and delete all files nested under it</span></span>

<span data-ttu-id="ccad2-173">次に、新しいプロジェクトを追加し、エンティティ クラスを生成するつもりは</span><span class="sxs-lookup"><span data-stu-id="ccad2-173">Next, we are going to add a new project and generate the entity classes in it</span></span>

-   <span data-ttu-id="ccad2-174">**ファイル -&gt;追加 -&gt;プロジェクト.**</span><span class="sxs-lookup"><span data-stu-id="ccad2-174">**File -&gt; Add -&gt; Project...**</span></span>
-   <span data-ttu-id="ccad2-175">選択**Visual C\#** 左側のウィンドウからし**クラス ライブラリ**</span><span class="sxs-lookup"><span data-stu-id="ccad2-175">Select **Visual C\#** from the left pane and then **Class Library**</span></span>
-   <span data-ttu-id="ccad2-176">入力**STESample.Entities**名をクリックします**OK**</span><span class="sxs-lookup"><span data-stu-id="ccad2-176">Enter **STESample.Entities** as the name and click **OK**</span></span>
-   <span data-ttu-id="ccad2-177">**プロジェクト -&gt;既存の項目を追加しています.**</span><span class="sxs-lookup"><span data-stu-id="ccad2-177">**Project -&gt; Add Existing Item...**</span></span>
-   <span data-ttu-id="ccad2-178">移動し、 **STESample**プロジェクト フォルダー</span><span class="sxs-lookup"><span data-stu-id="ccad2-178">Navigate to the **STESample** project folder</span></span>
-   <span data-ttu-id="ccad2-179">表示する選択**すべてのファイル (\*.\*)**</span><span class="sxs-lookup"><span data-stu-id="ccad2-179">Select to view **All Files (\*.\*)**</span></span>
-   <span data-ttu-id="ccad2-180">選択、 **STETemplate.tt**ファイル</span><span class="sxs-lookup"><span data-stu-id="ccad2-180">Select the **STETemplate.tt** file</span></span>
-   <span data-ttu-id="ccad2-181">次に、ドロップダウン矢印をクリックして、**追加**ボタンをクリックし、選択**リンクとして追加**</span><span class="sxs-lookup"><span data-stu-id="ccad2-181">Click on the drop down arrow next to the **Add** button and select **Add As Link**</span></span>

    ![AddLinkedTemplate](~/ef6/media/addlinkedtemplate.png)

<span data-ttu-id="ccad2-183">今回は、コンテキストと同じ名前空間で生成されたエンティティ クラスになっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ccad2-183">We're also going to make sure the entity classes get generated in the same namespace as the context.</span></span> <span data-ttu-id="ccad2-184">これだけのステートメントは、アプリケーション全体で追加する必要がありますを使用して数を減らします。</span><span class="sxs-lookup"><span data-stu-id="ccad2-184">This just reduces the number of using statements we need to add throughout our application.</span></span>

-   <span data-ttu-id="ccad2-185">リンクを右クリックして**STETemplate.tt**で**ソリューション エクスプ ローラー**選択**プロパティ**</span><span class="sxs-lookup"><span data-stu-id="ccad2-185">Right-click on the linked **STETemplate.tt** in **Solution Explorer** and select **Properties**</span></span>
-   <span data-ttu-id="ccad2-186">**プロパティ**ウィンドウ セット**カスタム ツールの Namespace**に**STESample**</span><span class="sxs-lookup"><span data-stu-id="ccad2-186">In the **Properties** window set **Custom Tool Namespace** to **STESample**</span></span>

<span data-ttu-id="ccad2-187">STE テンプレートによって生成されたコードへの参照は必要があります**System.Runtime.Serialization**コンパイルするためにします。</span><span class="sxs-lookup"><span data-stu-id="ccad2-187">The code generated by the STE template will need a reference to **System.Runtime.Serialization** in order to compile.</span></span> <span data-ttu-id="ccad2-188">このライブラリは、WCF に必要な**DataContract**と**DataMember**シリアル化可能なエンティティ型で使用される属性。</span><span class="sxs-lookup"><span data-stu-id="ccad2-188">This library is needed for the WCF **DataContract** and **DataMember** attributes that are used on the serializable entity types.</span></span>

-   <span data-ttu-id="ccad2-189">右クリックして、 **STESample.Entities**プロジェクト**ソリューション エクスプ ローラー**選択**参照の追加.**</span><span class="sxs-lookup"><span data-stu-id="ccad2-189">Right click on the **STESample.Entities** project in **Solution Explorer** and select **Add Reference...**</span></span>
    -   <span data-ttu-id="ccad2-190">Visual Studio 2012 - チェック ボックスの横に**System.Runtime.Serialization**クリック**OK**</span><span class="sxs-lookup"><span data-stu-id="ccad2-190">In Visual Studio 2012 - check the box next to **System.Runtime.Serialization** and click **OK**</span></span>
    -   <span data-ttu-id="ccad2-191">Visual Studio 2010 では、次のように選択します**System.Runtime.Serialization**クリック **[ok]。**</span><span class="sxs-lookup"><span data-stu-id="ccad2-191">In Visual Studio 2010 - select **System.Runtime.Serialization** and click **OK**</span></span>

<span data-ttu-id="ccad2-192">最後に、これで、コンテキストを使用してプロジェクトには、エンティティ型への参照を必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccad2-192">Finally, the project with our context in it will need a reference to the entity types.</span></span>

-   <span data-ttu-id="ccad2-193">右クリックして、 **STESample**プロジェクト**ソリューション エクスプ ローラー**選択**参照の追加.**</span><span class="sxs-lookup"><span data-stu-id="ccad2-193">Right click on the **STESample** project in **Solution Explorer** and select **Add Reference...**</span></span>
    -   <span data-ttu-id="ccad2-194">Visual Studio 2012 - 選択**ソリューション**横にチェック ボックスをオン、左側のウィンドウから**STESample.Entities**  をクリック**ok**</span><span class="sxs-lookup"><span data-stu-id="ccad2-194">In Visual Studio 2012 - select **Solution** from the left pane, check the box next to **STESample.Entities** and click **OK**</span></span>
    -   <span data-ttu-id="ccad2-195">Visual Studio 2010 での選択、**プロジェクト** タブで  **STESample.Entities**  をクリック**OK**</span><span class="sxs-lookup"><span data-stu-id="ccad2-195">In Visual Studio 2010 - select the **Projects** tab, select **STESample.Entities** and click **OK**</span></span>

>[!NOTE]
> <span data-ttu-id="ccad2-196">エンティティ型を別のプロジェクトに移動するためのもう 1 つのオプションでは、既定の場所からリンクすることではなく、テンプレート ファイルを移動します。</span><span class="sxs-lookup"><span data-stu-id="ccad2-196">Another option for moving the entity types to a separate project is to move the template file, rather than linking it from its default location.</span></span> <span data-ttu-id="ccad2-197">これを行うと、更新する必要があります、 **inputFile** edmx ファイルへの相対パスを提供するテンプレートの変数 (はこの例では **..\\BloggingModel.edmx**)。</span><span class="sxs-lookup"><span data-stu-id="ccad2-197">If you do this, you will need to update the **inputFile** variable in the template to provide the relative path to the edmx file (in this example that would be **..\\BloggingModel.edmx**).</span></span>

## <a name="create-a-wcf-service"></a><span data-ttu-id="ccad2-198">WCF サービスを作成します。</span><span class="sxs-lookup"><span data-stu-id="ccad2-198">Create a WCF Service</span></span>

<span data-ttu-id="ccad2-199">データを公開する WCF サービスを追加するときは、これで、プロジェクトの作成から始めます。</span><span class="sxs-lookup"><span data-stu-id="ccad2-199">Now it's time to add a WCF Service to expose our data, we'll start by creating the project.</span></span>

-   <span data-ttu-id="ccad2-200">**ファイル -&gt;追加 -&gt;プロジェクト.**</span><span class="sxs-lookup"><span data-stu-id="ccad2-200">**File -&gt; Add -&gt; Project...**</span></span>
-   <span data-ttu-id="ccad2-201">選択**Visual C\#** 左側のウィンドウからし**WCF サービス アプリケーション**</span><span class="sxs-lookup"><span data-stu-id="ccad2-201">Select **Visual C\#** from the left pane and then **WCF Service Application**</span></span>
-   <span data-ttu-id="ccad2-202">入力**STESample.Service**名をクリックします**OK**</span><span class="sxs-lookup"><span data-stu-id="ccad2-202">Enter **STESample.Service** as the name and click **OK**</span></span>
-   <span data-ttu-id="ccad2-203">参照を追加、 **System.Data.Entity**アセンブリ</span><span class="sxs-lookup"><span data-stu-id="ccad2-203">Add a reference to the **System.Data.Entity** assembly</span></span>
-   <span data-ttu-id="ccad2-204">参照を追加、 **STESample**と**STESample.Entities**プロジェクト</span><span class="sxs-lookup"><span data-stu-id="ccad2-204">Add a reference to the **STESample** and **STESample.Entities** projects</span></span>

<span data-ttu-id="ccad2-205">実行時に見つかったように、このプロジェクトに EF 接続文字列をコピーする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccad2-205">We need to copy the EF connection string to this project so that it is found at runtime.</span></span>

-   <span data-ttu-id="ccad2-206">開く、 **App.Config**ファイルを * * STESample * * プロジェクトとコピー、 **connectionStrings**要素</span><span class="sxs-lookup"><span data-stu-id="ccad2-206">Open the **App.Config** file for the **STESample **project and copy the **connectionStrings** element</span></span>
-   <span data-ttu-id="ccad2-207">貼り付け、 **connectionStrings**要素の子要素として、**構成**の要素、 **Web.Config**ファイル、 **STESample.Service**プロジェクト</span><span class="sxs-lookup"><span data-stu-id="ccad2-207">Paste the **connectionStrings** element as a child element of the **configuration** element of the **Web.Config** file in the **STESample.Service** project</span></span>

<span data-ttu-id="ccad2-208">ここには、実際のサービスを実装します。</span><span class="sxs-lookup"><span data-stu-id="ccad2-208">Now it's time to implement the actual service.</span></span>

-   <span data-ttu-id="ccad2-209">開いている**IService1.cs**内容を次のコードに置き換えます</span><span class="sxs-lookup"><span data-stu-id="ccad2-209">Open **IService1.cs** and replace the contents with the following code</span></span>

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

-   <span data-ttu-id="ccad2-210">開いている**Service1.svc**内容を次のコードに置き換えます</span><span class="sxs-lookup"><span data-stu-id="ccad2-210">Open **Service1.svc** and replace the contents with the following code</span></span>

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

## <a name="consume-the-service-from-a-console-application"></a><span data-ttu-id="ccad2-211">コンソール アプリケーションからサービスを使用します。</span><span class="sxs-lookup"><span data-stu-id="ccad2-211">Consume the Service from a Console Application</span></span>

<span data-ttu-id="ccad2-212">サービスを使用するコンソール アプリケーションを作成しましょう。</span><span class="sxs-lookup"><span data-stu-id="ccad2-212">Let's create a console application that uses our service.</span></span>

-   <span data-ttu-id="ccad2-213">**ファイル -&gt;新機能 -&gt;プロジェクト.**</span><span class="sxs-lookup"><span data-stu-id="ccad2-213">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="ccad2-214">選択**Visual C\#** 左側のウィンドウからし**コンソール アプリケーション**</span><span class="sxs-lookup"><span data-stu-id="ccad2-214">Select **Visual C\#** from the left pane and then **Console Application**</span></span>
-   <span data-ttu-id="ccad2-215">入力**STESample.ConsoleTest**名をクリックします**OK**</span><span class="sxs-lookup"><span data-stu-id="ccad2-215">Enter **STESample.ConsoleTest** as the name and click **OK**</span></span>
-   <span data-ttu-id="ccad2-216">参照を追加、 **STESample.Entities**プロジェクト</span><span class="sxs-lookup"><span data-stu-id="ccad2-216">Add a reference to the **STESample.Entities** project</span></span>

<span data-ttu-id="ccad2-217">WCF サービスにサービス参照を必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccad2-217">We need a service reference to our WCF service</span></span>

-   <span data-ttu-id="ccad2-218">右クリックし、 **STESample.ConsoleTest**プロジェクト**ソリューション エクスプ ローラー**選択**サービス参照の追加.**</span><span class="sxs-lookup"><span data-stu-id="ccad2-218">Right-click the **STESample.ConsoleTest** project in **Solution Explorer** and select **Add Service Reference...**</span></span>
-   <span data-ttu-id="ccad2-219">クリックして**検出**</span><span class="sxs-lookup"><span data-stu-id="ccad2-219">Click **Discover**</span></span>
-   <span data-ttu-id="ccad2-220">入力**BloggingService**名前空間をクリックします**OK**</span><span class="sxs-lookup"><span data-stu-id="ccad2-220">Enter **BloggingService** as the namespace and click **OK**</span></span>

<span data-ttu-id="ccad2-221">サービスを使用するいくつかのコードを書きます。</span><span class="sxs-lookup"><span data-stu-id="ccad2-221">Now we can write some code to consume the service.</span></span>

-   <span data-ttu-id="ccad2-222">開いている**Program.cs**内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="ccad2-222">Open **Program.cs** and replace the contents with the following code.</span></span>

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

<span data-ttu-id="ccad2-223">アプリケーションを実行して動作を確認できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="ccad2-223">You can now run the application to see it in action.</span></span>

-   <span data-ttu-id="ccad2-224">右クリックし、 **STESample.ConsoleTest**プロジェクト**ソリューション エクスプ ローラー**選択**デバッグ -&gt;新しいインスタンスを開始**</span><span class="sxs-lookup"><span data-stu-id="ccad2-224">Right-click the **STESample.ConsoleTest** project in **Solution Explorer** and select **Debug -&gt; Start new instance**</span></span>

<span data-ttu-id="ccad2-225">アプリケーションを実行するときは、次の出力を確認します。</span><span class="sxs-lookup"><span data-stu-id="ccad2-225">You'll see the following output when the application executes.</span></span>

```
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

## <a name="consume-the-service-from-a-wpf-application"></a><span data-ttu-id="ccad2-226">WPF アプリケーションからサービスを使用します。</span><span class="sxs-lookup"><span data-stu-id="ccad2-226">Consume the Service from a WPF Application</span></span>

<span data-ttu-id="ccad2-227">サービスを使用する WPF アプリケーションを作成しましょう。</span><span class="sxs-lookup"><span data-stu-id="ccad2-227">Let's create a WPF application that uses our service.</span></span>

-   <span data-ttu-id="ccad2-228">**ファイル -&gt;新機能 -&gt;プロジェクト.**</span><span class="sxs-lookup"><span data-stu-id="ccad2-228">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="ccad2-229">選択**Visual C\#** 左側のウィンドウからし**WPF アプリケーション**</span><span class="sxs-lookup"><span data-stu-id="ccad2-229">Select **Visual C\#** from the left pane and then **WPF Application**</span></span>
-   <span data-ttu-id="ccad2-230">入力**STESample.WPFTest**名をクリックします**OK**</span><span class="sxs-lookup"><span data-stu-id="ccad2-230">Enter **STESample.WPFTest** as the name and click **OK**</span></span>
-   <span data-ttu-id="ccad2-231">参照を追加、 **STESample.Entities**プロジェクト</span><span class="sxs-lookup"><span data-stu-id="ccad2-231">Add a reference to the **STESample.Entities** project</span></span>

<span data-ttu-id="ccad2-232">WCF サービスにサービス参照を必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccad2-232">We need a service reference to our WCF service</span></span>

-   <span data-ttu-id="ccad2-233">右クリックし、 **STESample.WPFTest**プロジェクト**ソリューション エクスプ ローラー**選択**サービス参照の追加.**</span><span class="sxs-lookup"><span data-stu-id="ccad2-233">Right-click the **STESample.WPFTest** project in **Solution Explorer** and select **Add Service Reference...**</span></span>
-   <span data-ttu-id="ccad2-234">クリックして**検出**</span><span class="sxs-lookup"><span data-stu-id="ccad2-234">Click **Discover**</span></span>
-   <span data-ttu-id="ccad2-235">入力**BloggingService**名前空間をクリックします**OK**</span><span class="sxs-lookup"><span data-stu-id="ccad2-235">Enter **BloggingService** as the namespace and click **OK**</span></span>

<span data-ttu-id="ccad2-236">サービスを使用するいくつかのコードを書きます。</span><span class="sxs-lookup"><span data-stu-id="ccad2-236">Now we can write some code to consume the service.</span></span>

-   <span data-ttu-id="ccad2-237">開いている**MainWindow.xaml**内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="ccad2-237">Open **MainWindow.xaml** and replace the contents with the following code.</span></span>

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

-   <span data-ttu-id="ccad2-238">MainWindow の分離コードを開きます (**MainWindow.xaml.cs**)、内容を次のコードに置き換えます</span><span class="sxs-lookup"><span data-stu-id="ccad2-238">Open the code behind for MainWindow (**MainWindow.xaml.cs**) and replace the contents with the following code</span></span>

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

<span data-ttu-id="ccad2-239">アプリケーションを実行して動作を確認できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="ccad2-239">You can now run the application to see it in action.</span></span>

-   <span data-ttu-id="ccad2-240">右クリックし、 **STESample.WPFTest**プロジェクト**ソリューション エクスプ ローラー**選択**デバッグ -&gt;新しいインスタンスを開始**</span><span class="sxs-lookup"><span data-stu-id="ccad2-240">Right-click the **STESample.WPFTest** project in **Solution Explorer** and select **Debug -&gt; Start new instance**</span></span>
-   <span data-ttu-id="ccad2-241">画面を使用してデータを操作してを介してサービスを使用して、保存、**保存**ボタン</span><span class="sxs-lookup"><span data-stu-id="ccad2-241">You can manipulate the data using the screen and save it via the service using the **Save** button</span></span>

![WPF](~/ef6/media/wpf.png)
