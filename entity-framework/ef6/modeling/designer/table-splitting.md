---
title: デザイナーのテーブル分割-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 452f17c3-9f26-4de4-9894-8bc036e23b0f
ms.openlocfilehash: f5e7532e6c0b473d8ce77cbd11e3e673b0af6cbe
ms.sourcegitcommit: b2b9468de2cf930687f8b85c3ce54ff8c449f644
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70921776"
---
# <a name="designer-table-splitting"></a><span data-ttu-id="b377f-102">デザイナーのテーブル分割</span><span class="sxs-lookup"><span data-stu-id="b377f-102">Designer Table Splitting</span></span>
<span data-ttu-id="b377f-103">このチュートリアルでは、Entity Framework Designer (EF Designer) を使用してモデルを変更することで、複数のエンティティ型を1つのテーブルにマップする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="b377f-103">This walkthrough shows how to map multiple entity types to a single table by modifying a model with the Entity Framework Designer (EF Designer).</span></span>

<span data-ttu-id="b377f-104">テーブル分割を使用する理由の1つとして、遅延読み込みを使用してオブジェクトを読み込むときに、一部のプロパティの読み込みが遅れることがあります。</span><span class="sxs-lookup"><span data-stu-id="b377f-104">One reason you may want to use table splitting is delaying the loading of some properties when using lazy loading to load your objects.</span></span><span data-ttu-id="b377f-105"> 非常に大量のデータを含む可能性のあるプロパティを別のエンティティに分割し、必要な場合にのみ読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="b377f-105"> You can separate the properties that might contain very large amount of data into a separate entity and only load it when required.</span></span>

<span data-ttu-id="b377f-106">次の図は、EF デザイナーを操作するときに使用されるメインウィンドウを示しています。</span><span class="sxs-lookup"><span data-stu-id="b377f-106">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF デザイナー](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a><span data-ttu-id="b377f-108">前提条件</span><span class="sxs-lookup"><span data-stu-id="b377f-108">Prerequisites</span></span>

<span data-ttu-id="b377f-109">このチュートリアルを完了するための要件を次に示します。</span><span class="sxs-lookup"><span data-stu-id="b377f-109">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="b377f-110">Visual Studio の最新バージョン。</span><span class="sxs-lookup"><span data-stu-id="b377f-110">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="b377f-111">[School サンプルデータベース](~/ef6/resources/school-database.md)。</span><span class="sxs-lookup"><span data-stu-id="b377f-111">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="b377f-112">プロジェクトを設定する</span><span class="sxs-lookup"><span data-stu-id="b377f-112">Set up the Project</span></span>

<span data-ttu-id="b377f-113">このチュートリアルでは、Visual Studio 2012 を使用します。</span><span class="sxs-lookup"><span data-stu-id="b377f-113">This walkthrough is using Visual Studio 2012.</span></span>

-   <span data-ttu-id="b377f-114">Visual Studio 2012 を開きます。</span><span class="sxs-lookup"><span data-stu-id="b377f-114">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="b377f-115">**[ファイル]** メニューの **[新規作成]** をポイントし、 **[プロジェクト]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b377f-115">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="b377f-116">左側のウィンドウで、[Visual C\#] をクリックし、[コンソールアプリケーション] テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="b377f-116">In the left pane, click Visual C\#, and then select the Console Application template.</span></span>
-   <span data-ttu-id="b377f-117">プロジェクトの名前として「[]」**と入力し**、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b377f-117">Enter **TableSplittingSample** as the name of the project and click **OK**.</span></span>

## <a name="create-a-model-based-on-the-school-database"></a><span data-ttu-id="b377f-118">School データベースに基づくモデルを作成する</span><span class="sxs-lookup"><span data-stu-id="b377f-118">Create a Model based on the School Database</span></span>

-   <span data-ttu-id="b377f-119">ソリューションエクスプローラーでプロジェクト名を右クリックして **[追加]** をポイントし、 **[新しい項目]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b377f-119">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="b377f-120">左側のメニューから **[データ]** を選択し、テンプレート ペインで  **[ADO.NET Entity Data Model]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b377f-120">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="b377f-121">ファイル名として「」**と入力し**、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b377f-121">Enter **TableSplittingModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="b377f-122">[モデルのコンテンツの選択] ダイアログボックスで、[ **データベースから生成**] を選択し、[次へ] をクリックし **ます。**</span><span class="sxs-lookup"><span data-stu-id="b377f-122">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next.**</span></span>
-   <span data-ttu-id="b377f-123">[新しい接続] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b377f-123">Click New Connection.</span></span> <span data-ttu-id="b377f-124">[接続プロパティ] ダイアログボックスで、サーバー名 (たとえば、 **(localdb)\\mssqllocaldb**) を入力し、認証方法を選択します。データベース名として「 **School** 」と入力し、[ **OK**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b377f-124">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="b377f-125">[データ接続の選択] ダイアログボックスが、データベース接続の設定によって更新されます。</span><span class="sxs-lookup"><span data-stu-id="b377f-125">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="b377f-126">[データベースオブジェクトの選択] ダイアログボックスで、[**テーブル** ] ノードを展開し、 **[Person]** テーブルを確認します。</span><span class="sxs-lookup"><span data-stu-id="b377f-126">In the Choose Your Database Objects dialog box, unfold the **Tables** node and check the **Person** table.</span></span> <span data-ttu-id="b377f-127">これにより、指定したテーブルが**School**モデルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="b377f-127">This will add the specified table to the **School** model.</span></span>
-   <span data-ttu-id="b377f-128">[ **完了**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b377f-128">Click **Finish**.</span></span>

<span data-ttu-id="b377f-129">モデルを編集するためのデザイン画面を提供する Entity Designer が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b377f-129">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="b377f-130">[ **データベースオブジェクトの選択** ] ダイアログボックスで選択したすべてのオブジェクトがモデルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="b377f-130">All the objects that you selected in the **Choose Your Database Objects** dialog box are added to the model.</span></span>

## <a name="map-two-entities-to-a-single-table"></a><span data-ttu-id="b377f-131">2つのエンティティを1つのテーブルにマップする</span><span class="sxs-lookup"><span data-stu-id="b377f-131">Map Two Entities to a Single Table</span></span>

<span data-ttu-id="b377f-132">このセクションでは、 **Person**エンティティを2つのエンティティに分割し、1つのテーブルにマップします。</span><span class="sxs-lookup"><span data-stu-id="b377f-132">In this section you will split the **Person** entity into two entities and then map them to a single table.</span></span>

> [!NOTE]
> <span data-ttu-id="b377f-133">**Person**エンティティには、大量のデータを含む可能性のあるプロパティが含まれていません。例として使用されています。</span><span class="sxs-lookup"><span data-stu-id="b377f-133">The **Person** entity does not contain any properties that may contain large amount of data; it is just used as an example.</span></span>

-   <span data-ttu-id="b377f-134">デザイン画面の空の領域を右クリックし、[ **新規追加**] をポイントして、[ **エンティティ**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b377f-134">Right-click an empty area of the design surface, point to **Add New**, and click **Entity**.</span></span>
    <span data-ttu-id="b377f-135">[ **新しいエンティティ** ] ダイアログボックスが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b377f-135">The **New Entity** dialog box appears.</span></span>
-   <span data-ttu-id="b377f-136">[ **エンティティ名**] に「 **hireinfo** 」と入力し、**キープロパティ**名に「 **PersonID** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="b377f-136">Type **HireInfo** for the **Entity name** and **PersonID** for the **Key Property** name.</span></span>
-   <span data-ttu-id="b377f-137">[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b377f-137">Click **OK**.</span></span>
-   <span data-ttu-id="b377f-138">新しいエンティティ型が作成され、デザイン画面に表示されます。</span><span class="sxs-lookup"><span data-stu-id="b377f-138">A new entity type is created and displayed on the design surface.</span></span>
-   <span data-ttu-id="b377f-139"> **Person** エンティティ型の **HireDate** プロパティを選択し、ctrl キーを押し**ながら X**キーを押します。</span><span class="sxs-lookup"><span data-stu-id="b377f-139">Select the **HireDate** property of the **Person** entity type and press **Ctrl+X** keys.</span></span>
-   <span data-ttu-id="b377f-140">**Hireinfo** エンティティを選択し、ctrl キーを押し**ながら V**キーを押します。</span><span class="sxs-lookup"><span data-stu-id="b377f-140">Select the **HireInfo** entity and press **Ctrl+V** keys.</span></span>
-   <span data-ttu-id="b377f-141">**Person**と**hireinfo**の間の関連付けを作成します。</span><span class="sxs-lookup"><span data-stu-id="b377f-141">Create an association between **Person** and **HireInfo**.</span></span> <span data-ttu-id="b377f-142">これを行うには、デザイン画面の空の領域を右クリックし、[ **新規追加**] をポイントして、[ **関連付け**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b377f-142">To do this, right-click an empty area of the design surface, point to **Add New**, and click **Association**.</span></span>
-   <span data-ttu-id="b377f-143">[ **関連付け**の の追加] ダイアログボックスが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b377f-143">The **Add Association** dialog box appears.</span></span> <span data-ttu-id="b377f-144">既定では、**個人情報**の名前が指定されています。</span><span class="sxs-lookup"><span data-stu-id="b377f-144">The **PersonHireInfo** name is given by default.</span></span>
-   <span data-ttu-id="b377f-145">リレーションシップの両方の end で多重度**1 (1)** を指定します。</span><span class="sxs-lookup"><span data-stu-id="b377f-145">Specify multiplicity **1(One)** on both ends of the relationship.</span></span>
-   <span data-ttu-id="b377f-146">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b377f-146">Press **OK**.</span></span>

<span data-ttu-id="b377f-147">次の手順では、[ **マッピングの詳細** ] ウィンドウが必要です。</span><span class="sxs-lookup"><span data-stu-id="b377f-147">The next step requires the **Mapping Details** window.</span></span> <span data-ttu-id="b377f-148">このウィンドウが表示されない場合は、デザイン画面を右クリックし、 **[マッピングの詳細]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b377f-148">If you cannot see this window, right-click the design surface and select **Mapping Details**.</span></span>

-   <span data-ttu-id="b377f-149"> **Hireinfo** エンティティ型を選択し、 **マッピングの詳細**  ウィンドウで  **テーブルまたはビューを追加**します&gt; &lt;クリックします。</span><span class="sxs-lookup"><span data-stu-id="b377f-149">Select the **HireInfo** entity type and click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="b377f-150">[**テーブルまたはビュー&gt;**  フィールド] ボックス&lt;の一覧から **[個人]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b377f-150">Select **Person** from the **&lt;Add a Table or View&gt;** field drop-down list.</span></span> <span data-ttu-id="b377f-151">この一覧には、選択したエンティティをマップできるテーブルまたはビューが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b377f-151">The list contains tables or views to which the selected entity can be mapped.</span></span>
    <span data-ttu-id="b377f-152">既定では、適切なプロパティをマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b377f-152">The appropriate properties should be mapped by default.</span></span>

    ![マッピング](~/ef6/media/mapping.png)

-   <span data-ttu-id="b377f-154">デザイン画面で、 **[個人情報]** の関連付けを選択します。</span><span class="sxs-lookup"><span data-stu-id="b377f-154">Select the **PersonHireInfo** association on the design surface.</span></span>
-   <span data-ttu-id="b377f-155">デザイン画面でアソシエーションを右クリックし、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b377f-155">Right-click the association on the design surface and select **Properties**.</span></span>
-   <span data-ttu-id="b377f-156">**[プロパティ]** ウィンドウで、 **[参照]** に関する制約 プロパティを選択し、省略記号ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="b377f-156">In the **Properties** window, select the **Referential Constraints** property and click the ellipses button.</span></span>
-   <span data-ttu-id="b377f-157">**[プリンシパル]** ドロップダウンリストから **[Person]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b377f-157">Select **Person** from the **Principal** drop-down list.</span></span>
-   <span data-ttu-id="b377f-158">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b377f-158">Press **OK**.</span></span>

 

## <a name="use-the-model"></a><span data-ttu-id="b377f-159">モデルを使用する</span><span class="sxs-lookup"><span data-stu-id="b377f-159">Use the Model</span></span>

-   <span data-ttu-id="b377f-160">Main メソッドに次のコードを貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="b377f-160">Paste the following code in the Main method.</span></span>

``` csharp
    using (var context = new SchoolEntities())
    {
        Person person = new Person()
        {
            FirstName = "Kimberly",
            LastName = "Morgan",
            Discriminator = "Instructor",
        };

        person.HireInfo = new HireInfo()
        {
            HireDate = DateTime.Now
        };

        // Add the new person to the context.
        context.People.Add(person);

        // Insert a row into the Person table.  
        context.SaveChanges();

        // Execute a query against the Person table.
        // The query returns columns that map to the Person entity.
        var existingPerson = context.People.FirstOrDefault();

        // Execute a query against the Person table.
        // The query returns columns that map to the Instructor entity.
        var hireInfo = existingPerson.HireInfo;

        Console.WriteLine("{0} was hired on {1}",
            existingPerson.LastName, hireInfo.HireDate);
    }
```
-   <span data-ttu-id="b377f-161">アプリケーションをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="b377f-161">Compile and run the application.</span></span>

<span data-ttu-id="b377f-162">このアプリケーションを実行した結果、 **School**データベースに対して次の t-sql ステートメントが実行されました。</span><span class="sxs-lookup"><span data-stu-id="b377f-162">The following T-SQL statements were executed against the **School** database as a result of running this application.</span></span> 

-   <span data-ttu-id="b377f-163">次の**INSERT**は、コンテキストの実行結果として実行されました。SaveChanges () と**Person**および**hireinfo**エンティティのデータを結合します。</span><span class="sxs-lookup"><span data-stu-id="b377f-163">The following **INSERT** was executed as a result of executing context.SaveChanges() and combines data from the **Person** and **HireInfo** entities</span></span>

    ![［挿入］](~/ef6/media/insert.png)

-   <span data-ttu-id="b377f-165">次の**SELECT**は、コンテキストの実行結果として実行されました。FirstOrDefault () を選択し、 **Person**にマップされた列のみを選択します</span><span class="sxs-lookup"><span data-stu-id="b377f-165">The following **SELECT** was executed as a result of executing context.People.FirstOrDefault() and selects just the columns mapped to **Person**</span></span>

    ![1を選択します](~/ef6/media/select1.png)

-   <span data-ttu-id="b377f-167">次の**SELECT**は、ナビゲーションプロパティ existingperson にアクセスした結果として実行され、 **hireinfo**にマップされた列のみを選択しました。</span><span class="sxs-lookup"><span data-stu-id="b377f-167">The following **SELECT** was executed as a result of accessing the navigation property existingPerson.Instructor and selects just the columns mapped to **HireInfo**</span></span>

    ![2を選択](~/ef6/media/select2.png)
