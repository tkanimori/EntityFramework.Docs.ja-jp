---
title: デザイナーエンティティ分割-EF6
description: Entity Framework 6 でのデザイナーエンティティの分割
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/entity-splitting
ms.openlocfilehash: fa25629bae1a35b2d792e2b890e87db6c99c37ab
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073137"
---
# <a name="designer-entity-splitting"></a><span data-ttu-id="d968f-103">デザイナーエンティティの分割</span><span class="sxs-lookup"><span data-stu-id="d968f-103">Designer Entity Splitting</span></span>
<span data-ttu-id="d968f-104">このチュートリアルでは、Entity Framework Designer (EF Designer) を使用してモデルを変更することによって、エンティティ型を2つのテーブルにマップする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="d968f-104">This walkthrough shows how to map an entity type to two tables by modifying a model with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="d968f-105">エンティティを複数のテーブルにマップできるのは、それらのテーブルのキーが共通している場合です。</span><span class="sxs-lookup"><span data-stu-id="d968f-105">You can map an entity to multiple tables when the tables share a common key.</span></span> <span data-ttu-id="d968f-106">エンティティ型を 2 つのテーブルにマップする場合に適用される概念は、エンティティ型を 3 つ以上のテーブルにマップする場合にも簡単に応用できます。</span><span class="sxs-lookup"><span data-stu-id="d968f-106">The concepts that apply to mapping an entity type to two tables are easily extended to mapping an entity type to more than two tables.</span></span>

<span data-ttu-id="d968f-107">次の図は、EF デザイナーを操作するときに使用されるメインウィンドウを示しています。</span><span class="sxs-lookup"><span data-stu-id="d968f-107">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF デザイナー](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a><span data-ttu-id="d968f-109">前提条件</span><span class="sxs-lookup"><span data-stu-id="d968f-109">Prerequisites</span></span>

<span data-ttu-id="d968f-110">Visual Studio 2012 または Visual Studio 2010、Ultimate、Premium、Professional、または Web Express edition。</span><span class="sxs-lookup"><span data-stu-id="d968f-110">Visual Studio 2012 or Visual Studio 2010, Ultimate, Premium, Professional, or Web Express edition.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="d968f-111">データベースを作成する</span><span class="sxs-lookup"><span data-stu-id="d968f-111">Create the Database</span></span>

<span data-ttu-id="d968f-112">Visual Studio と共にインストールされるデータベースサーバーは、インストールされている Visual Studio のバージョンによって異なります。</span><span class="sxs-lookup"><span data-stu-id="d968f-112">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="d968f-113">Visual Studio 2012 を使用している場合は、LocalDB データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="d968f-113">If you are using Visual Studio 2012 then you'll be creating a LocalDB database.</span></span>
-   <span data-ttu-id="d968f-114">Visual Studio 2010 を使用している場合は、SQL Express データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="d968f-114">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>

<span data-ttu-id="d968f-115">まず、1つのエンティティに結合する2つのテーブルを含むデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="d968f-115">First we'll create a database with two tables that we are going to combine into a single entity.</span></span>

-   <span data-ttu-id="d968f-116">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="d968f-116">Open Visual Studio</span></span>
-   <span data-ttu-id="d968f-117">**ビュー- &gt; サーバーエクスプローラー**</span><span class="sxs-lookup"><span data-stu-id="d968f-117">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="d968f-118">[**データ接続-接続の &gt; 追加**] を右クリックします。</span><span class="sxs-lookup"><span data-stu-id="d968f-118">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="d968f-119">サーバーエクスプローラーからデータベースに接続していない場合は、データソースとして **Microsoft SQL Server** を選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d968f-119">If you haven’t connected to a database from Server Explorer before you’ll need to select **Microsoft SQL Server** as the data source</span></span>
-   <span data-ttu-id="d968f-120">インストールされているものに応じて、LocalDB または SQL Express に接続します。</span><span class="sxs-lookup"><span data-stu-id="d968f-120">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>
-   <span data-ttu-id="d968f-121">データベース名として「 **EntitySplitting** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="d968f-121">Enter **EntitySplitting** as the database name</span></span>
-   <span data-ttu-id="d968f-122">[ **OK]** を選択すると、新しいデータベースを作成するかどうかを確認するメッセージが表示されます。 [**はい**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="d968f-122">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="d968f-123">新しいデータベースがに表示されるようになりサーバーエクスプローラー</span><span class="sxs-lookup"><span data-stu-id="d968f-123">The new database will now appear in Server Explorer</span></span>
-   <span data-ttu-id="d968f-124">Visual Studio 2012 を使用している場合</span><span class="sxs-lookup"><span data-stu-id="d968f-124">If you are using Visual Studio 2012</span></span>
    -   <span data-ttu-id="d968f-125">[サーバー エクスプローラー] でこのデータベースを右クリックし、**[新しいクエリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d968f-125">Right-click on the database in Server Explorer and select **New Query**</span></span>
    -   <span data-ttu-id="d968f-126">次の SQL を新しいクエリにコピーし、クエリを右クリックして、[**実行**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="d968f-126">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>
-   <span data-ttu-id="d968f-127">Visual Studio 2010 を使用している場合</span><span class="sxs-lookup"><span data-stu-id="d968f-127">If you are using Visual Studio 2010</span></span>
    -   <span data-ttu-id="d968f-128">[**データの選択]-[ &gt; transact-sql エディター]-[ &gt; 新しいクエリ接続**]</span><span class="sxs-lookup"><span data-stu-id="d968f-128">Select **Data -&gt; Transact SQL Editor -&gt; New Query Connection...**</span></span>
    -   <span data-ttu-id="d968f-129">「」と入力**します。 \\**サーバー名として SQLEXPRESS を指定し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d968f-129">Enter **.\\SQLEXPRESS** as the server name and click **OK**</span></span>
    -   <span data-ttu-id="d968f-130">クエリエディターの上部にあるドロップダウンから **EntitySplitting** データベースを選択します。</span><span class="sxs-lookup"><span data-stu-id="d968f-130">Select the **EntitySplitting** database from the drop down at the top of the query editor</span></span>
    -   <span data-ttu-id="d968f-131">次の SQL を新しいクエリにコピーし、クエリを右クリックして、[ **sql の実行**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="d968f-131">Copy the following SQL into the new query, then right-click on the query and select **Execute SQL**</span></span>

``` SQL
CREATE TABLE [dbo].[Person] (
[PersonId] INT IDENTITY (1, 1) NOT NULL,
[FirstName] NVARCHAR (200) NULL,
[LastName] NVARCHAR (200) NULL,
CONSTRAINT [PK_Person] PRIMARY KEY CLUSTERED ([PersonId] ASC)
);

CREATE TABLE [dbo].[PersonInfo] (
[PersonId] INT NOT NULL,
[Email] NVARCHAR (200) NULL,
[Phone] NVARCHAR (50) NULL,
CONSTRAINT [PK_PersonInfo] PRIMARY KEY CLUSTERED ([PersonId] ASC),
CONSTRAINT [FK_Person_PersonInfo] FOREIGN KEY ([PersonId]) REFERENCES [dbo].[Person] ([PersonId]) ON DELETE CASCADE
);
```

## <a name="create-the-project"></a><span data-ttu-id="d968f-132">プロジェクトの作成</span><span class="sxs-lookup"><span data-stu-id="d968f-132">Create the Project</span></span>

-   <span data-ttu-id="d968f-133">**[ファイル]** メニューの **[新規作成]** をポイントし、 **[プロジェクト]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d968f-133">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="d968f-134">左側のウィンドウで、[ **Visual C \# **] をクリックし、[**コンソールアプリケーション**] テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="d968f-134">In the left pane, click **Visual C\#**, and then select the **Console Application** template.</span></span>
-   <span data-ttu-id="d968f-135">プロジェクトの名前として「 **Mapentityを** 入力し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d968f-135">Enter **MapEntityToTablesSample** as the name of the project and click **OK**.</span></span>
-   <span data-ttu-id="d968f-136">最初のセクションで作成した SQL クエリの保存を確認するメッセージが表示されたら、[ **いいえ** ] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d968f-136">Click **No** if prompted to save the SQL query created in the first section.</span></span>

## <a name="create-a-model-based-on-the-database"></a><span data-ttu-id="d968f-137">データベースに基づいてモデルを作成する</span><span class="sxs-lookup"><span data-stu-id="d968f-137">Create a Model based on the Database</span></span>

-   <span data-ttu-id="d968f-138">ソリューションエクスプローラーでプロジェクト名を右クリックして [ **追加**] をポイントし、[ **新しい項目**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d968f-138">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="d968f-139">左側のメニューから [ **データ** ] を選択し、[テンプレート] ペインで [ **ADO.NET Entity Data Model** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="d968f-139">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="d968f-140">ファイル名として「 **MapEntityToTablesModel** 」と入力し、[ **追加**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d968f-140">Enter **MapEntityToTablesModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="d968f-141">[モデルのコンテンツの選択] ダイアログボックスで、[ **データベースから生成**] を選択し、[次へ] をクリックし **ます。**</span><span class="sxs-lookup"><span data-stu-id="d968f-141">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next.**</span></span>
-   <span data-ttu-id="d968f-142">ドロップダウンから **EntitySplitting** 接続を選択し、[ **次へ**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d968f-142">Select the **EntitySplitting** connection from the drop down and click **Next**.</span></span>
-   <span data-ttu-id="d968f-143">[データベースオブジェクトの選択] ダイアログボックスで、[**テーブル**] ノードの横にあるチェックボックスをオンにし   ます。</span><span class="sxs-lookup"><span data-stu-id="d968f-143">In the Choose Your Database Objects dialog box, check the box next to the **Tables** node.</span></span>
    <span data-ttu-id="d968f-144">これにより、 **EntitySplitting** データベースのすべてのテーブルがモデルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="d968f-144">This will add all the tables from the **EntitySplitting** database to the model.</span></span>
-   <span data-ttu-id="d968f-145">[ **完了**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d968f-145">Click **Finish**.</span></span>

<span data-ttu-id="d968f-146">モデルを編集するためのデザイン画面を提供する Entity Designer が表示されます。</span><span class="sxs-lookup"><span data-stu-id="d968f-146">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

## <a name="map-an-entity-to-two-tables"></a><span data-ttu-id="d968f-147">2つのテーブルへのエンティティのマップ</span><span class="sxs-lookup"><span data-stu-id="d968f-147">Map an Entity to Two Tables</span></span>

<span data-ttu-id="d968f-148">この手順で **は、person エンティティ型** を更新して、 **person** テーブルと個人 **情報** テーブルのデータを結合します。</span><span class="sxs-lookup"><span data-stu-id="d968f-148">In this step we will update the **Person** entity type to combine data from the **Person** and **PersonInfo** tables.</span></span>

-   <span data-ttu-id="d968f-149"> **Email**    **個人情報 **エンティティの電子メールと**電話**のプロパティを選択し、ctrl キーを押し**ながら X**キーを押します。</span><span class="sxs-lookup"><span data-stu-id="d968f-149">Select the **Email** and **Phone** properties of the **PersonInfo **entity and press **Ctrl+X** keys.</span></span>
-   <span data-ttu-id="d968f-150"> **Person **エンティティを選択し、ctrl キーを押し**ながら V**キーを押します。</span><span class="sxs-lookup"><span data-stu-id="d968f-150">Select the **Person **entity and press **Ctrl+V** keys.</span></span>
-   <span data-ttu-id="d968f-151">デザイン画面で、[ **個人情報**] エンティティを選択   し、キーボードの**del**キーを押します。</span><span class="sxs-lookup"><span data-stu-id="d968f-151">On the design surface, select the **PersonInfo** entity and press **Delete** button on the keyboard.</span></span>
-   <span data-ttu-id="d968f-152">モデルから個人**情報**テーブルを削除するかどうかを確認するメッセージが表示されたら、[**いいえ**] をクリックします。これは**Person**エンティティにマップしようとしています。</span><span class="sxs-lookup"><span data-stu-id="d968f-152">Click **No** when asked if you want to remove the **PersonInfo** table from the model, we are about to map it to the **Person** entity.</span></span>

    ![テーブルの削除](~/ef6/media/deletetables.png)

<span data-ttu-id="d968f-154">次の手順では、[ **マッピングの詳細**   ] ウィンドウが必要です。</span><span class="sxs-lookup"><span data-stu-id="d968f-154">The next steps require the **Mapping Details** window.</span></span> <span data-ttu-id="d968f-155">このウィンドウが表示されない場合は、デザイン画面を右クリックし、[ **マッピングの詳細**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="d968f-155">If you cannot see this window, right-click the design surface and select **Mapping Details**.</span></span>

-   <span data-ttu-id="d968f-156"> **Person**   エンティティ型を選択し、[マッピングの詳細] ウィンドウで [\ \** &lt; テーブルまたは &gt; ビューの追加**] をクリックし    **Mapping Detail\s\**   ます。</span><span class="sxs-lookup"><span data-stu-id="d968f-156">Select the **Person** entity type and click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="d968f-157">ドロップダウンリストから [ **個人情報 **] を選択し   ます。</span><span class="sxs-lookup"><span data-stu-id="d968f-157">Select **PersonInfo ** from the drop-down list.</span></span>
    <span data-ttu-id="d968f-158">[ **マッピングの詳細**   ] ウィンドウは、既定の列マッピングで更新されます。これは、このシナリオでは問題ありません。</span><span class="sxs-lookup"><span data-stu-id="d968f-158">The **Mapping Details** window is updated with default column mappings, these are fine for our scenario.</span></span>

<span data-ttu-id="d968f-159"> **Person**   エンティティ型は、person テーブルと個人 **Person**    **情報**テーブルにマップされるようになりました   。</span><span class="sxs-lookup"><span data-stu-id="d968f-159">The **Person** entity type is now mapped to the **Person** and **PersonInfo** tables.</span></span>

![マッピング2](~/ef6/media/mapping2.png)

## <a name="use-the-model"></a><span data-ttu-id="d968f-161">モデルを使用する</span><span class="sxs-lookup"><span data-stu-id="d968f-161">Use the Model</span></span>

-   <span data-ttu-id="d968f-162">Main メソッドに次のコードを貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="d968f-162">Paste the following code in the Main method.</span></span>

``` csharp
    using (var context = new EntitySplittingEntities())
    {
        var person = new Person
        {
            FirstName = "John",
            LastName = "Doe",
            Email = "john@example.com",
            Phone = "555-555-5555"
        };

        context.People.Add(person);
        context.SaveChanges();

        foreach (var item in context.People)
        {
            Console.WriteLine(item.FirstName);
        }
    }
```

-   <span data-ttu-id="d968f-163">アプリケーションをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="d968f-163">Compile and run the application.</span></span>

<span data-ttu-id="d968f-164">このアプリケーションを実行した結果、データベースに対して次の T-sql ステートメントが実行されました。</span><span class="sxs-lookup"><span data-stu-id="d968f-164">The following T-SQL statements were executed against the database as a result of running this application.</span></span> 

-   <span data-ttu-id="d968f-165">次の2つの **INSERT** ステートメントは、コンテキストの実行結果として実行されました。SaveChanges ()。</span><span class="sxs-lookup"><span data-stu-id="d968f-165">The following two **INSERT** statements were executed as a result of executing context.SaveChanges().</span></span> <span data-ttu-id="d968f-166">**Person**エンティティからデータを取得し、 **person**テーブルと個人**情報**テーブルの間で分割します。</span><span class="sxs-lookup"><span data-stu-id="d968f-166">They take the data from the **Person** entity and split it between the **Person** and **PersonInfo** tables.</span></span>

    ![挿入1](~/ef6/media/insert1.png)

    ![2の挿入](~/ef6/media/insert2.png)
-   <span data-ttu-id="d968f-169">次の **SELECT** は、データベース内の people を列挙した結果として実行されました。</span><span class="sxs-lookup"><span data-stu-id="d968f-169">The following **SELECT** was executed as a result of enumerating the people in the database.</span></span> <span data-ttu-id="d968f-170">**Person**テーブルと個人**情報**テーブルのデータを結合します。</span><span class="sxs-lookup"><span data-stu-id="d968f-170">It combines the data from the **Person** and **PersonInfo** table.</span></span>

    ![個人情報と個人情報データの組み合わせを選択する](~/ef6/media/select.png)
