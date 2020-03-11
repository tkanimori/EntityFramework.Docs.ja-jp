---
title: デザイナーエンティティ分割-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: aa2dd48a-1f0e-49dd-863d-d6b4f5834832
ms.openlocfilehash: ba1895ae491cec909ff88a8784eea82f1876f595
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415263"
---
# <a name="designer-entity-splitting"></a><span data-ttu-id="0bf49-102">デザイナーエンティティの分割</span><span class="sxs-lookup"><span data-stu-id="0bf49-102">Designer Entity Splitting</span></span>
<span data-ttu-id="0bf49-103">このチュートリアルでは、Entity Framework Designer (EF Designer) を使用してモデルを変更することによって、エンティティ型を2つのテーブルにマップする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="0bf49-103">This walkthrough shows how to map an entity type to two tables by modifying a model with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="0bf49-104">エンティティを複数のテーブルにマップできるのは、それらのテーブルのキーが共通している場合です。</span><span class="sxs-lookup"><span data-stu-id="0bf49-104">You can map an entity to multiple tables when the tables share a common key.</span></span> <span data-ttu-id="0bf49-105">エンティティ型を2つのテーブルにマップする場合に適用される概念は、エンティティ型を3つ以上のテーブルにマップするために簡単に拡張できます。</span><span class="sxs-lookup"><span data-stu-id="0bf49-105">The concepts that apply to mapping an entity type to two tables are easily extended to mapping an entity type to more than two tables.</span></span>

<span data-ttu-id="0bf49-106">次の図は、EF デザイナーを操作するときに使用されるメインウィンドウを示しています。</span><span class="sxs-lookup"><span data-stu-id="0bf49-106">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF デザイナー](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a><span data-ttu-id="0bf49-108">前提条件</span><span class="sxs-lookup"><span data-stu-id="0bf49-108">Prerequisites</span></span>

<span data-ttu-id="0bf49-109">Visual Studio 2012 または Visual Studio 2010、Ultimate、Premium、Professional、または Web Express edition。</span><span class="sxs-lookup"><span data-stu-id="0bf49-109">Visual Studio 2012 or Visual Studio 2010, Ultimate, Premium, Professional, or Web Express edition.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="0bf49-110">データベースを作成する</span><span class="sxs-lookup"><span data-stu-id="0bf49-110">Create the Database</span></span>

<span data-ttu-id="0bf49-111">Visual Studio と共にインストールされるデータベースサーバーは、インストールされている Visual Studio のバージョンによって異なります。</span><span class="sxs-lookup"><span data-stu-id="0bf49-111">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="0bf49-112">Visual Studio 2012 を使用している場合は、LocalDB データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="0bf49-112">If you are using Visual Studio 2012 then you'll be creating a LocalDB database.</span></span>
-   <span data-ttu-id="0bf49-113">Visual Studio 2010 を使用している場合は、SQL Express データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="0bf49-113">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>

<span data-ttu-id="0bf49-114">まず、1つのエンティティに結合する2つのテーブルを含むデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="0bf49-114">First we'll create a database with two tables that we are going to combine into a single entity.</span></span>

-   <span data-ttu-id="0bf49-115">Visual Studio を開きます</span><span class="sxs-lookup"><span data-stu-id="0bf49-115">Open Visual Studio</span></span>
-   <span data-ttu-id="0bf49-116">**ビュー&gt; サーバーエクスプローラー**</span><span class="sxs-lookup"><span data-stu-id="0bf49-116">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="0bf49-117">**[データ接続]** を右クリックし&gt; [接続の追加] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="0bf49-117">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="0bf49-118">サーバーエクスプローラーからデータベースに接続していない場合は、データソースとして**Microsoft SQL Server**を選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0bf49-118">If you haven’t connected to a database from Server Explorer before you’ll need to select **Microsoft SQL Server** as the data source</span></span>
-   <span data-ttu-id="0bf49-119">インストールされているものに応じて、LocalDB または SQL Express に接続します。</span><span class="sxs-lookup"><span data-stu-id="0bf49-119">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>
-   <span data-ttu-id="0bf49-120">データベース名として「 **EntitySplitting** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="0bf49-120">Enter **EntitySplitting** as the database name</span></span>
-   <span data-ttu-id="0bf49-121">[ **OK]** を選択すると、新しいデータベースを作成するかどうかを確認するメッセージが表示されます。 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0bf49-121">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="0bf49-122">新しいデータベースがに表示されるようになりサーバーエクスプローラー</span><span class="sxs-lookup"><span data-stu-id="0bf49-122">The new database will now appear in Server Explorer</span></span>
-   <span data-ttu-id="0bf49-123">Visual Studio 2012 を使用している場合</span><span class="sxs-lookup"><span data-stu-id="0bf49-123">If you are using Visual Studio 2012</span></span>
    -   <span data-ttu-id="0bf49-124">サーバーエクスプローラーでデータベースを右クリックし、 **[新しいクエリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0bf49-124">Right-click on the database in Server Explorer and select **New Query**</span></span>
    -   <span data-ttu-id="0bf49-125">次の SQL を新しいクエリにコピーし、クエリを右クリックして、 **[実行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0bf49-125">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>
-   <span data-ttu-id="0bf49-126">Visual Studio 2010 を使用している場合</span><span class="sxs-lookup"><span data-stu-id="0bf49-126">If you are using Visual Studio 2010</span></span>
    -   <span data-ttu-id="0bf49-127">[データ] を選択し **&gt; TRANSACT SQL エディター-&gt; 新しいクエリ接続...**</span><span class="sxs-lookup"><span data-stu-id="0bf49-127">Select **Data -&gt; Transact SQL Editor -&gt; New Query Connection...**</span></span>
    -   <span data-ttu-id="0bf49-128">「」と入力し、サーバー名として\\SQLEXPRESS を入力し、[ **OK]** をクリックし**ます。**</span><span class="sxs-lookup"><span data-stu-id="0bf49-128">Enter **.\\SQLEXPRESS** as the server name and click **OK**</span></span>
    -   <span data-ttu-id="0bf49-129">クエリエディターの上部にあるドロップダウンから**EntitySplitting**データベースを選択します。</span><span class="sxs-lookup"><span data-stu-id="0bf49-129">Select the **EntitySplitting** database from the drop down at the top of the query editor</span></span>
    -   <span data-ttu-id="0bf49-130">次の SQL を新しいクエリにコピーし、クエリを右クリックして、 **[sql の実行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0bf49-130">Copy the following SQL into the new query, then right-click on the query and select **Execute SQL**</span></span>

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

## <a name="create-the-project"></a><span data-ttu-id="0bf49-131">プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="0bf49-131">Create the Project</span></span>

-   <span data-ttu-id="0bf49-132">**[ファイル]** メニューの **[新規作成]** をポイントし、 **[プロジェクト]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="0bf49-132">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="0bf49-133">左側のウィンドウで、[ **Visual C\#** ] をクリックし、 **[コンソールアプリケーション]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="0bf49-133">In the left pane, click **Visual C\#**, and then select the **Console Application** template.</span></span>
-   <span data-ttu-id="0bf49-134">プロジェクトの名前として「 **Mapentityを**入力し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="0bf49-134">Enter **MapEntityToTablesSample** as the name of the project and click **OK**.</span></span>
-   <span data-ttu-id="0bf49-135">最初のセクションで作成した SQL クエリの保存を確認するメッセージが表示されたら、 **[いいえ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="0bf49-135">Click **No** if prompted to save the SQL query created in the first section.</span></span>

## <a name="create-a-model-based-on-the-database"></a><span data-ttu-id="0bf49-136">データベースに基づいてモデルを作成する</span><span class="sxs-lookup"><span data-stu-id="0bf49-136">Create a Model based on the Database</span></span>

-   <span data-ttu-id="0bf49-137">ソリューションエクスプローラーでプロジェクト名を右クリックして **[追加]** をポイントし、 **[新しい項目]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="0bf49-137">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="0bf49-138">左側のメニューから **[データ]** を選択し、テンプレート ペインで  **[ADO.NET Entity Data Model]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0bf49-138">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="0bf49-139">ファイル名として「 **MapEntityToTablesModel** 」と入力し、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="0bf49-139">Enter **MapEntityToTablesModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="0bf49-140">[モデルのコンテンツの選択] ダイアログボックスで、[ **データベースから生成**] を選択し、[次へ] をクリックし **ます。**</span><span class="sxs-lookup"><span data-stu-id="0bf49-140">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next.**</span></span>
-   <span data-ttu-id="0bf49-141">ドロップダウンから**EntitySplitting**接続を選択し、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="0bf49-141">Select the **EntitySplitting** connection from the drop down and click **Next**.</span></span>
-   <span data-ttu-id="0bf49-142">[データベースオブジェクトの選択] ダイアログボックスで、[**テーブル** ] ノードの横にあるチェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="0bf49-142">In the Choose Your Database Objects dialog box, check the box next to the **Tables** node.</span></span>
    <span data-ttu-id="0bf49-143">これにより、 **EntitySplitting**データベースのすべてのテーブルがモデルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="0bf49-143">This will add all the tables from the **EntitySplitting** database to the model.</span></span>
-   <span data-ttu-id="0bf49-144">[ **完了**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="0bf49-144">Click **Finish**.</span></span>

<span data-ttu-id="0bf49-145">モデルを編集するためのデザイン画面を提供する Entity Designer が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0bf49-145">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

## <a name="map-an-entity-to-two-tables"></a><span data-ttu-id="0bf49-146">2つのテーブルへのエンティティのマップ</span><span class="sxs-lookup"><span data-stu-id="0bf49-146">Map an Entity to Two Tables</span></span>

<span data-ttu-id="0bf49-147">この手順で**は、person エンティティ型**を更新して、 **person**テーブルと個人**情報**テーブルのデータを結合します。</span><span class="sxs-lookup"><span data-stu-id="0bf49-147">In this step we will update the **Person** entity type to combine data from the **Person** and **PersonInfo** tables.</span></span>

-   <span data-ttu-id="0bf49-148"> **[個人情報] ** エンティティの [ **Email** ] および **[Phone]** プロパティを選択し、ctrl キーを押し**ながら X**キーを押します。</span><span class="sxs-lookup"><span data-stu-id="0bf49-148">Select the **Email** and **Phone** properties of the **PersonInfo **entity and press **Ctrl+X** keys.</span></span>
-   <span data-ttu-id="0bf49-149"> **Person **エンティティを選択し、ctrl キーを押し**ながら V**キーを押します。</span><span class="sxs-lookup"><span data-stu-id="0bf49-149">Select the **Person **entity and press **Ctrl+V** keys.</span></span>
-   <span data-ttu-id="0bf49-150">デザイン画面で、[ **個人情報** ] エンティティを選択し、キーボードの**del**キーを押します。</span><span class="sxs-lookup"><span data-stu-id="0bf49-150">On the design surface, select the **PersonInfo** entity and press **Delete** button on the keyboard.</span></span>
-   <span data-ttu-id="0bf49-151">モデルから個人**情報**テーブルを削除するかどうかを確認するメッセージが表示されたら、 **[いいえ]** をクリックします。これは**Person**エンティティにマップしようとしています。</span><span class="sxs-lookup"><span data-stu-id="0bf49-151">Click **No** when asked if you want to remove the **PersonInfo** table from the model, we are about to map it to the **Person** entity.</span></span>

    ![テーブルの削除](~/ef6/media/deletetables.png)

<span data-ttu-id="0bf49-153">次の手順では、[ **マッピングの詳細** ] ウィンドウが必要です。</span><span class="sxs-lookup"><span data-stu-id="0bf49-153">The next steps require the **Mapping Details** window.</span></span> <span data-ttu-id="0bf49-154">このウィンドウが表示されない場合は、デザイン画面を右クリックし、 **[マッピングの詳細]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0bf49-154">If you cannot see this window, right-click the design surface and select **Mapping Details**.</span></span>

-   <span data-ttu-id="0bf49-155">[ **Person** ] エンティティ型を選択し、[ **マッピングの詳細** ] ウィンドウで [ **テーブルまたはビューを追加&lt;&gt;**  ] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="0bf49-155">Select the **Person** entity type and click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="0bf49-156">ドロップダウンリストから [ **個人情報 ** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="0bf49-156">Select **PersonInfo ** from the drop-down list.</span></span>
    <span data-ttu-id="0bf49-157">[ **マッピングの詳細** ] ウィンドウは、既定の列マッピングで更新されます。このシナリオでは問題ありません。</span><span class="sxs-lookup"><span data-stu-id="0bf49-157">The **Mapping Details** window is updated with default column mappings, these are fine for our scenario.</span></span>

<span data-ttu-id="0bf49-158"> **Person** エンティティ型が、テーブル  **person** および個人 **情報**にマップされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="0bf49-158">The **Person** entity type is now mapped to the **Person** and **PersonInfo** tables.</span></span>

![マッピング2](~/ef6/media/mapping2.png)

## <a name="use-the-model"></a><span data-ttu-id="0bf49-160">モデルを使用する</span><span class="sxs-lookup"><span data-stu-id="0bf49-160">Use the Model</span></span>

-   <span data-ttu-id="0bf49-161">Main メソッドに次のコードを貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="0bf49-161">Paste the following code in the Main method.</span></span>

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

-   <span data-ttu-id="0bf49-162">アプリケーションをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="0bf49-162">Compile and run the application.</span></span>

<span data-ttu-id="0bf49-163">このアプリケーションを実行した結果、データベースに対して次の T-sql ステートメントが実行されました。</span><span class="sxs-lookup"><span data-stu-id="0bf49-163">The following T-SQL statements were executed against the database as a result of running this application.</span></span> 

-   <span data-ttu-id="0bf49-164">次の2つの**INSERT**ステートメントは、コンテキストの実行結果として実行されました。SaveChanges ()。</span><span class="sxs-lookup"><span data-stu-id="0bf49-164">The following two **INSERT** statements were executed as a result of executing context.SaveChanges().</span></span> <span data-ttu-id="0bf49-165">**Person**エンティティからデータを取得し、 **person**テーブルと個人**情報**テーブルの間で分割します。</span><span class="sxs-lookup"><span data-stu-id="0bf49-165">They take the data from the **Person** entity and split it between the **Person** and **PersonInfo** tables.</span></span>

    ![挿入1](~/ef6/media/insert1.png)

    ![2の挿入](~/ef6/media/insert2.png)
-   <span data-ttu-id="0bf49-168">次の**SELECT**は、データベース内の people を列挙した結果として実行されました。</span><span class="sxs-lookup"><span data-stu-id="0bf49-168">The following **SELECT** was executed as a result of enumerating the people in the database.</span></span> <span data-ttu-id="0bf49-169">**Person**テーブルと個人**情報**テーブルのデータを結合します。</span><span class="sxs-lookup"><span data-stu-id="0bf49-169">It combines the data from the **Person** and **PersonInfo** table.</span></span>

    ![選択](~/ef6/media/select.png)
