---
title: エンティティ デザイナーの分割の EF6
author: divega
ms.date: 2016-10-23
ms.assetid: aa2dd48a-1f0e-49dd-863d-d6b4f5834832
ms.openlocfilehash: 214561f0a0381bced3ceae0b6acfcd45f5dd65c5
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995620"
---
# <a name="designer-entity-splitting"></a><span data-ttu-id="b26c4-102">デザイナーのエンティティを分割</span><span class="sxs-lookup"><span data-stu-id="b26c4-102">Designer Entity Splitting</span></span>
<span data-ttu-id="b26c4-103">このチュートリアルでは、Entity Framework デザイナー (EF Designer) を持つモデルを変更することで、エンティティ型を 2 つのテーブルにマップする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="b26c4-103">This walkthrough shows how to map an entity type to two tables by modifying a model with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="b26c4-104">エンティティを複数のテーブルにマップできるのは、それらのテーブルのキーが共通している場合です。</span><span class="sxs-lookup"><span data-stu-id="b26c4-104">You can map an entity to multiple tables when the tables share a common key.</span></span> <span data-ttu-id="b26c4-105">エンティティ型を 2 つのテーブルにマップする場合に適用される概念は、エンティティ型を 3 つ以上のテーブルにマップする場合にも簡単に応用できます。</span><span class="sxs-lookup"><span data-stu-id="b26c4-105">The concepts that apply to mapping an entity type to two tables are easily extended to mapping an entity type to more than two tables.</span></span>

<span data-ttu-id="b26c4-106">次の図は、EF Designer を使用する場合に使用される主なウィンドウを示します。</span><span class="sxs-lookup"><span data-stu-id="b26c4-106">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EFDesigner](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a><span data-ttu-id="b26c4-108">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="b26c4-108">Prerequisites</span></span>

<span data-ttu-id="b26c4-109">Visual Studio 2012 または Visual Studio 2010、Ultimate、Premium、Professional、または Web Express のエディションです。</span><span class="sxs-lookup"><span data-stu-id="b26c4-109">Visual Studio 2012 or Visual Studio 2010, Ultimate, Premium, Professional, or Web Express edition.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="b26c4-110">データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="b26c4-110">Create the Database</span></span>

<span data-ttu-id="b26c4-111">Visual Studio と共にインストールされているデータベース サーバーは、インストールした Visual Studio のバージョンによって異なります。</span><span class="sxs-lookup"><span data-stu-id="b26c4-111">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="b26c4-112">Visual Studio 2012 を使用している場合、作成する LocalDB データベース。</span><span class="sxs-lookup"><span data-stu-id="b26c4-112">If you are using Visual Studio 2012 then you'll be creating a LocalDB database.</span></span>
-   <span data-ttu-id="b26c4-113">Visual Studio 2010 を使用している場合は、SQL Express データベースを作成するがします。</span><span class="sxs-lookup"><span data-stu-id="b26c4-113">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>

<span data-ttu-id="b26c4-114">まずここでは結合する 1 つのエンティティに 2 つのテーブルでデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="b26c4-114">First we'll create a database with two tables that we are going to combine into a single entity.</span></span>

-   <span data-ttu-id="b26c4-115">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="b26c4-115">Open Visual Studio</span></span>
-   <span data-ttu-id="b26c4-116">**ビュー -&gt;サーバー エクスプ ローラー**</span><span class="sxs-lookup"><span data-stu-id="b26c4-116">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="b26c4-117">右クリックして**データ接続 -&gt;接続の追加.**</span><span class="sxs-lookup"><span data-stu-id="b26c4-117">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="b26c4-118">まだデータベースに接続して、サーバー エクスプ ローラーから選択する必要があります前に場合**Microsoft SQL Server**データ ソースとして</span><span class="sxs-lookup"><span data-stu-id="b26c4-118">If you haven’t connected to a database from Server Explorer before you’ll need to select **Microsoft SQL Server** as the data source</span></span>
-   <span data-ttu-id="b26c4-119">LocalDB または SQL Express をインストールしたものによってのいずれかに接続します。</span><span class="sxs-lookup"><span data-stu-id="b26c4-119">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>
-   <span data-ttu-id="b26c4-120">入力**EntitySplitting**データベース名として</span><span class="sxs-lookup"><span data-stu-id="b26c4-120">Enter **EntitySplitting** as the database name</span></span>
-   <span data-ttu-id="b26c4-121">選択**OK**かどうかは、新しいデータベースを作成するように要求がある **[はい]**</span><span class="sxs-lookup"><span data-stu-id="b26c4-121">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="b26c4-122">新しいデータベースがサーバー エクスプ ローラーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="b26c4-122">The new database will now appear in Server Explorer</span></span>
-   <span data-ttu-id="b26c4-123">Visual Studio 2012 を使用している場合</span><span class="sxs-lookup"><span data-stu-id="b26c4-123">If you are using Visual Studio 2012</span></span>
    -   <span data-ttu-id="b26c4-124">サーバー エクスプ ローラーでデータベースを右クリックし、選択**新しいクエリ**</span><span class="sxs-lookup"><span data-stu-id="b26c4-124">Right-click on the database in Server Explorer and select **New Query**</span></span>
    -   <span data-ttu-id="b26c4-125">新しいクエリに、次の SQL をコピーし、クエリを選択します右クリックし、 **Execute**</span><span class="sxs-lookup"><span data-stu-id="b26c4-125">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>
-   <span data-ttu-id="b26c4-126">Visual Studio 2010 を使用している場合</span><span class="sxs-lookup"><span data-stu-id="b26c4-126">If you are using Visual Studio 2010</span></span>
    -   <span data-ttu-id="b26c4-127">選択**データ -&gt; Transact SQL エディター -&gt;新しいクエリ接続しています.**</span><span class="sxs-lookup"><span data-stu-id="b26c4-127">Select **Data -&gt; Transact SQL Editor -&gt; New Query Connection...**</span></span>
    -   <span data-ttu-id="b26c4-128">入力 **.\\SQLEXPRESS**サーバー名をクリックします**OK**</span><span class="sxs-lookup"><span data-stu-id="b26c4-128">Enter **.\\SQLEXPRESS** as the server name and click **OK**</span></span>
    -   <span data-ttu-id="b26c4-129">選択、 **EntitySplitting**クエリ エディターの上部にある下のドロップダウンからデータベース</span><span class="sxs-lookup"><span data-stu-id="b26c4-129">Select the **EntitySplitting** database from the drop down at the top of the query editor</span></span>
    -   <span data-ttu-id="b26c4-130">新しいクエリに、次の SQL をコピーし、クエリを選択します右クリックし、 **SQL の実行**</span><span class="sxs-lookup"><span data-stu-id="b26c4-130">Copy the following SQL into the new query, then right-click on the query and select **Execute SQL**</span></span>

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

## <a name="create-the-project"></a><span data-ttu-id="b26c4-131">プロジェクトの作成</span><span class="sxs-lookup"><span data-stu-id="b26c4-131">Create the Project</span></span>

-   <span data-ttu-id="b26c4-132">**[ファイル]** メニューの **[新規作成]** をポイントし、 **[プロジェクト]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b26c4-132">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="b26c4-133">左側のウィンドウで次のようにクリックします。 **Visual C\#** を選び、**コンソール アプリケーション**テンプレート。</span><span class="sxs-lookup"><span data-stu-id="b26c4-133">In the left pane, click **Visual C\#**, and then select the **Console Application** template.</span></span>
-   <span data-ttu-id="b26c4-134">入力**MapEntityToTablesSample**をクリックして、プロジェクトの名前として**OK**します。</span><span class="sxs-lookup"><span data-stu-id="b26c4-134">Enter **MapEntityToTablesSample** as the name of the project and click **OK**.</span></span>
-   <span data-ttu-id="b26c4-135">クリックして**いいえ**場合は、最初のセクションで作成した SQL クエリを保存するように求められます。</span><span class="sxs-lookup"><span data-stu-id="b26c4-135">Click **No** if prompted to save the SQL query created in the first section.</span></span>

## <a name="create-a-model-based-on-the-database"></a><span data-ttu-id="b26c4-136">データベースに基づくモデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="b26c4-136">Create a Model based on the Database</span></span>

-   <span data-ttu-id="b26c4-137">ソリューション エクスプ ローラーでプロジェクト名を右クリックし、[**追加**、] をクリックし、**新しい項目の**します。</span><span class="sxs-lookup"><span data-stu-id="b26c4-137">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="b26c4-138">選択**データ**選択し、左側のメニューから**ADO.NET Entity Data Model**テンプレート ペインでします。</span><span class="sxs-lookup"><span data-stu-id="b26c4-138">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="b26c4-139">入力**MapEntityToTablesModel.edmx**のファイル名、およびクリック**追加**します。</span><span class="sxs-lookup"><span data-stu-id="b26c4-139">Enter **MapEntityToTablesModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="b26c4-140">モデルのコンテンツの選択 ダイアログ ボックスで、次のように選択します。**データベースから生成**、 をクリックし、 **次へ。**</span><span class="sxs-lookup"><span data-stu-id="b26c4-140">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next.**</span></span>
-   <span data-ttu-id="b26c4-141">選択、 **EntitySplitting**ドロップダウンからの接続がダウンし、をクリックして**次**します。</span><span class="sxs-lookup"><span data-stu-id="b26c4-141">Select the **EntitySplitting** connection from the drop down and click **Next**.</span></span>
-   <span data-ttu-id="b26c4-142">データベース オブジェクトの選択 ダイアログ ボックスで、ボックスを横にチェック、**テーブル**ノード。</span><span class="sxs-lookup"><span data-stu-id="b26c4-142">In the Choose Your Database Objects dialog box, check the box next to the **Tables** node.</span></span>
    <span data-ttu-id="b26c4-143">これからのすべてのテーブルに追加されます、 **EntitySplitting**モデルへのデータベース。</span><span class="sxs-lookup"><span data-stu-id="b26c4-143">This will add all the tables from the **EntitySplitting** database to the model.</span></span>
-   <span data-ttu-id="b26c4-144">**[完了]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b26c4-144">Click **Finish**.</span></span>

<span data-ttu-id="b26c4-145">モデルを編集するため、デザイン サーフェイスを提供するエンティティ デザイナーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b26c4-145">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

## <a name="map-an-entity-to-two-tables"></a><span data-ttu-id="b26c4-146">エンティティを 2 つのテーブルにマップします。</span><span class="sxs-lookup"><span data-stu-id="b26c4-146">Map an Entity to Two Tables</span></span>

<span data-ttu-id="b26c4-147">この手順では更新、 **Person**からデータを結合するエンティティ型、 **Person**と**PersonInfo**テーブル。</span><span class="sxs-lookup"><span data-stu-id="b26c4-147">In this step we will update the **Person** entity type to combine data from the **Person** and **PersonInfo** tables.</span></span>

-   <span data-ttu-id="b26c4-148">選択、**電子メール**と**Phone**のプロパティ、* * PersonInfo * * エンティティ キーを押します**Ctrl + X**キー。</span><span class="sxs-lookup"><span data-stu-id="b26c4-148">Select the **Email** and **Phone** properties of the **PersonInfo **entity and press **Ctrl+X** keys.</span></span>
-   <span data-ttu-id="b26c4-149">選択、* * ユーザー * * エンティティ キーを押します**Ctrl + V**キー。</span><span class="sxs-lookup"><span data-stu-id="b26c4-149">Select the **Person **entity and press **Ctrl+V** keys.</span></span>
-   <span data-ttu-id="b26c4-150">デザイン サーフェイスでは、選択、 **PersonInfo**エンティティ キーを押します**削除**キーボードのキー。</span><span class="sxs-lookup"><span data-stu-id="b26c4-150">On the design surface, select the **PersonInfo** entity and press **Delete** button on the keyboard.</span></span>
-   <span data-ttu-id="b26c4-151">クリックして**いいえ**を削除するように求められたら、 **PersonInfo**テーブルにマップしようとしていますが、モデルから、**人**エンティティ。</span><span class="sxs-lookup"><span data-stu-id="b26c4-151">Click **No** when asked if you want to remove the **PersonInfo** table from the model, we are about to map it to the **Person** entity.</span></span>

    ![DeleteTables](~/ef6/media/deletetables.png)

<span data-ttu-id="b26c4-153">次の手順が必要です、**マッピングの詳細**ウィンドウ。</span><span class="sxs-lookup"><span data-stu-id="b26c4-153">The next steps require the **Mapping Details** window.</span></span> <span data-ttu-id="b26c4-154">このウィンドウを表示されない場合は、サーフェスと選択のデザインを右クリックして**マッピングの詳細**します。</span><span class="sxs-lookup"><span data-stu-id="b26c4-154">If you cannot see this window, right-click the design surface and select **Mapping Details**.</span></span>

-   <span data-ttu-id="b26c4-155">選択、 **Person**エンティティの種類をクリックします**&lt;テーブルまたはビューの追加&gt;** で、**マッピングの詳細**ウィンドウ。</span><span class="sxs-lookup"><span data-stu-id="b26c4-155">Select the **Person** entity type and click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="b26c4-156">選択 * * PersonInfo * * - ドロップダウン リストから。</span><span class="sxs-lookup"><span data-stu-id="b26c4-156">Select **PersonInfo ** from the drop-down list.</span></span>
    <span data-ttu-id="b26c4-157">**マッピングの詳細**ウィンドウが更新され、既定の列マッピング、これらは、ここでは問題ありません。</span><span class="sxs-lookup"><span data-stu-id="b26c4-157">The **Mapping Details** window is updated with default column mappings, these are fine for our scenario.</span></span>

<span data-ttu-id="b26c4-158">**Person**エンティティ型が現在にマップされている、 **Person**と**PersonInfo**テーブル。</span><span class="sxs-lookup"><span data-stu-id="b26c4-158">The **Person** entity type is now mapped to the **Person** and **PersonInfo** tables.</span></span>

![Mapping2](~/ef6/media/mapping2.png)

## <a name="use-the-model"></a><span data-ttu-id="b26c4-160">モデルを使用します。</span><span class="sxs-lookup"><span data-stu-id="b26c4-160">Use the Model</span></span>

-   <span data-ttu-id="b26c4-161">Main メソッドでは、次のコードを貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="b26c4-161">Paste the following code in the Main method.</span></span>

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

-   <span data-ttu-id="b26c4-162">アプリケーションをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="b26c4-162">Compile and run the application.</span></span>

<span data-ttu-id="b26c4-163">次の T-SQL ステートメントは、このアプリケーションを実行した結果、データベースに対して実行されました。</span><span class="sxs-lookup"><span data-stu-id="b26c4-163">The following T-SQL statements were executed against the database as a result of running this application.</span></span> 

-   <span data-ttu-id="b26c4-164">次の 2 つ**挿入**ステートメントのコンテキストを実行した結果として実行します。SaveChanges() します。</span><span class="sxs-lookup"><span data-stu-id="b26c4-164">The following two **INSERT** statements were executed as a result of executing context.SaveChanges().</span></span> <span data-ttu-id="b26c4-165">データを取得する、 **Person**エンティティの間で分割し、 **Person**と**PersonInfo**テーブル。</span><span class="sxs-lookup"><span data-stu-id="b26c4-165">They take the data from the **Person** entity and split it between the **Person** and **PersonInfo** tables.</span></span>

    ![Insert1](~/ef6/media/insert1.png)

    ![Insert2](~/ef6/media/insert2.png)
-   <span data-ttu-id="b26c4-168">次**選択**データベース内のユーザーの列挙の結果として実行されました。</span><span class="sxs-lookup"><span data-stu-id="b26c4-168">The following **SELECT** was executed as a result of enumerating the people in the database.</span></span> <span data-ttu-id="b26c4-169">データを組み合わせて、 **Person**と**PersonInfo**テーブル。</span><span class="sxs-lookup"><span data-stu-id="b26c4-169">It combines the data from the **Person** and **PersonInfo** table.</span></span>

    ![選択](~/ef6/media/select.png)
