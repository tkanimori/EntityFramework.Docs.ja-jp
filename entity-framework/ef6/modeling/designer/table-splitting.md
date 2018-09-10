---
title: デザイナーのテーブルは、次の分割の EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 452f17c3-9f26-4de4-9894-8bc036e23b0f
ms.openlocfilehash: f07aeb0aa679f6fa8131c667ac808f17c3f03f20
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2018
ms.locfileid: "44250986"
---
# <a name="designer-table-splitting"></a><span data-ttu-id="ef846-102">デザイナーのテーブルを分割</span><span class="sxs-lookup"><span data-stu-id="ef846-102">Designer Table Splitting</span></span>
<span data-ttu-id="ef846-103">このチュートリアルでは、Entity Framework デザイナー (EF Designer) を持つモデルを変更することで、複数のエンティティ型を 1 つのテーブルにマップする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="ef846-103">This walkthrough shows how to map multiple entity types to a single table by modifying a model with the Entity Framework Designer (EF Designer).</span></span>

<span data-ttu-id="ef846-104">テーブル分割を使用する理由の 1 つは、遅延読み込み、オブジェクトの読み込みに使用する場合、一部のプロパティの読み込みを遅らせることが。</span><span class="sxs-lookup"><span data-stu-id="ef846-104">One reason you may want to use table splitting is delaying the loading of some properties when using lazy loading to load your objects.</span></span> <span data-ttu-id="ef846-105">個別のエンティティに非常に大量のデータを含めることが必要な場合のみを読み込む可能性のあるプロパティを分離することができます。</span><span class="sxs-lookup"><span data-stu-id="ef846-105">You can separate the properties that might contain very large amount of data into a seperate entity and only load it when required.</span></span>

<span data-ttu-id="ef846-106">次の図は、EF Designer を使用する場合に使用される主なウィンドウを示します。</span><span class="sxs-lookup"><span data-stu-id="ef846-106">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF デザイナー](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a><span data-ttu-id="ef846-108">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="ef846-108">Prerequisites</span></span>

<span data-ttu-id="ef846-109">このチュートリアルを完了するための要件を次に示します。</span><span class="sxs-lookup"><span data-stu-id="ef846-109">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="ef846-110">Visual Studio の最新バージョン。</span><span class="sxs-lookup"><span data-stu-id="ef846-110">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="ef846-111">[School サンプル データベース](~/ef6/resources/school-database.md)します。</span><span class="sxs-lookup"><span data-stu-id="ef846-111">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="ef846-112">プロジェクトを設定します。</span><span class="sxs-lookup"><span data-stu-id="ef846-112">Set up the Project</span></span>

<span data-ttu-id="ef846-113">このチュートリアルでは、Visual Studio 2012 を使用しています。</span><span class="sxs-lookup"><span data-stu-id="ef846-113">This walkthrough is using Visual Studio 2012.</span></span>

-   <span data-ttu-id="ef846-114">Visual Studio 2012 を開きます。</span><span class="sxs-lookup"><span data-stu-id="ef846-114">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="ef846-115">**[ファイル]** メニューの **[新規作成]** をポイントし、 **[プロジェクト]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="ef846-115">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="ef846-116">左側のウィンドウで、Visual C をクリックします。\#、し、コンソール アプリケーション テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="ef846-116">In the left pane, click Visual C\#, and then select the Console Application template.</span></span>
-   <span data-ttu-id="ef846-117">入力**TableSplittingSample**をクリックして、プロジェクトの名前として**OK**します。</span><span class="sxs-lookup"><span data-stu-id="ef846-117">Enter **TableSplittingSample** as the name of the project and click **OK**.</span></span>

## <a name="create-a-model-based-on-the-school-database"></a><span data-ttu-id="ef846-118">School データベースに基づくモデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="ef846-118">Create a Model based on the School Database</span></span>

-   <span data-ttu-id="ef846-119">ソリューション エクスプ ローラーでプロジェクト名を右クリックし、[**追加**、] をクリックし、**新しい項目の**します。</span><span class="sxs-lookup"><span data-stu-id="ef846-119">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="ef846-120">選択**データ**選択し、左側のメニューから**ADO.NET Entity Data Model**テンプレート ペインでします。</span><span class="sxs-lookup"><span data-stu-id="ef846-120">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="ef846-121">入力**TableSplittingModel.edmx**のファイル名、およびクリック**追加**します。</span><span class="sxs-lookup"><span data-stu-id="ef846-121">Enter **TableSplittingModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="ef846-122">モデルのコンテンツの選択 ダイアログ ボックスで、次のように選択します。**データベースから生成**、 をクリックし、 **次へ。**</span><span class="sxs-lookup"><span data-stu-id="ef846-122">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next.**</span></span>
-   <span data-ttu-id="ef846-123">[新しい接続] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="ef846-123">Click New Connection.</span></span> <span data-ttu-id="ef846-124">接続のプロパティ ダイアログ ボックスで、サーバー名を入力します (たとえば、 **(localdb)\\mssqllocaldb**) を選択します、認証方式として、型**学校**データベース名、し、。クリックして**OK**します。</span><span class="sxs-lookup"><span data-stu-id="ef846-124">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="ef846-125">データ接続の選択 ダイアログ ボックスは、データベース接続の設定で更新されます。</span><span class="sxs-lookup"><span data-stu-id="ef846-125">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="ef846-126">データベース オブジェクトの選択 ダイアログ ボックスで、展開、**テーブル**ノードとチェック、 **Person**テーブル。</span><span class="sxs-lookup"><span data-stu-id="ef846-126">In the Choose Your Database Objects dialog box, unfold the **Tables** node and check the **Person** table.</span></span> <span data-ttu-id="ef846-127">これを指定したテーブルに追加されます、**学校**モデル。</span><span class="sxs-lookup"><span data-stu-id="ef846-127">This will add the specified table to the **School** model.</span></span>
-   <span data-ttu-id="ef846-128">**[完了]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="ef846-128">Click **Finish**.</span></span>

<span data-ttu-id="ef846-129">モデルを編集するため、デザイン サーフェイスを提供するエンティティ デザイナーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ef846-129">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="ef846-130">選択したすべてのオブジェクト、**データベース オブジェクトの選択** ダイアログ ボックスは、モデルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="ef846-130">All the objects that you selected in the **Choose Your Database Objects** dialog box are added to the model.</span></span>

## <a name="map-two-entities-to-a-single-table"></a><span data-ttu-id="ef846-131">2 つのエンティティを 1 つのテーブルにマップします。</span><span class="sxs-lookup"><span data-stu-id="ef846-131">Map Two Entities to a Single Table</span></span>

<span data-ttu-id="ef846-132">このセクションに分割する、 **Person** 2 つのエンティティにエンティティし、それらを 1 つのテーブルにマップします。</span><span class="sxs-lookup"><span data-stu-id="ef846-132">In this section you will split the **Person** entity into two entities and then map them to a single table.</span></span>

> [!NOTE]
> <span data-ttu-id="ef846-133">**Person**エンティティに大量のデータを含む可能性のある任意のプロパティが含まれていません。 例としてだけに使用されます。</span><span class="sxs-lookup"><span data-stu-id="ef846-133">The **Person** entity does not contain any properties that may contain large amount of data; it is just used as an example.</span></span>

-   <span data-ttu-id="ef846-134">デザイン サーフェイスの空の領域を右クリックし、[**新規追加**、] をクリック**エンティティ**します。</span><span class="sxs-lookup"><span data-stu-id="ef846-134">Right-click an empty area of the design surface, point to **Add New**, and click **Entity**.</span></span>
    <span data-ttu-id="ef846-135">**新しいエンティティ** ダイアログ ボックスが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ef846-135">The **New Entity** dialog box appears.</span></span>
-   <span data-ttu-id="ef846-136">型**HireInfo**の**エンティティ名**と**PersonID**の**キー プロパティ**名。</span><span class="sxs-lookup"><span data-stu-id="ef846-136">Type **HireInfo** for the **Entity name** and **PersonID** for the **Key Property** name.</span></span>
-   <span data-ttu-id="ef846-137">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="ef846-137">Click **OK**.</span></span>
-   <span data-ttu-id="ef846-138">新しいエンティティ型が作成され、デザイン画面に表示されます。</span><span class="sxs-lookup"><span data-stu-id="ef846-138">A new entity type is created and displayed on the design surface.</span></span>
-   <span data-ttu-id="ef846-139">選択、 **HireDate**のプロパティ、 **Person**エンティティ型とキーを押して**Ctrl + X**キー。</span><span class="sxs-lookup"><span data-stu-id="ef846-139">Select the **HireDate** property of the **Person** entity type and press **Ctrl+X** keys.</span></span>
-   <span data-ttu-id="ef846-140">選択、 **HireInfo**エンティティ キーを押します**Ctrl + V**キー。</span><span class="sxs-lookup"><span data-stu-id="ef846-140">Select the **HireInfo** entity and press **Ctrl+V** keys.</span></span>
-   <span data-ttu-id="ef846-141">間のアソシエーションを作成する**Person**と**HireInfo**します。</span><span class="sxs-lookup"><span data-stu-id="ef846-141">Create an association between **Person** and **HireInfo**.</span></span> <span data-ttu-id="ef846-142">これを行うには、デザイン サーフェイスの空の領域を右クリックし、 をポイント**新規追加**、 をクリック**アソシエーション**します。</span><span class="sxs-lookup"><span data-stu-id="ef846-142">To do this, right-click an empty area of the design surface, point to **Add New**, and click **Association**.</span></span>
-   <span data-ttu-id="ef846-143">**の関連付けの追加** ダイアログ ボックスが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ef846-143">The **Add Association** dialog box appears.</span></span> <span data-ttu-id="ef846-144">**PersonHireInfo**既定で名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="ef846-144">The **PersonHireInfo** name is given by default.</span></span>
-   <span data-ttu-id="ef846-145">多重度を指定**1(One)** リレーションシップの両端でします。</span><span class="sxs-lookup"><span data-stu-id="ef846-145">Specify multiplicity **1(One)** on both ends of the relationship.</span></span>
-   <span data-ttu-id="ef846-146">**[OK]** を押します。</span><span class="sxs-lookup"><span data-stu-id="ef846-146">Press **OK**.</span></span>

<span data-ttu-id="ef846-147">次の手順が必要です、**マッピングの詳細**ウィンドウ。</span><span class="sxs-lookup"><span data-stu-id="ef846-147">The next step requires the **Mapping Details** window.</span></span> <span data-ttu-id="ef846-148">このウィンドウを表示されない場合は、サーフェスと選択のデザインを右クリックして**マッピングの詳細**します。</span><span class="sxs-lookup"><span data-stu-id="ef846-148">If you cannot see this window, right-click the design surface and select **Mapping Details**.</span></span>

-   <span data-ttu-id="ef846-149">選択、 **HireInfo**エンティティの種類をクリックします**&lt;テーブルまたはビューの追加&gt;** で、**マッピングの詳細**ウィンドウ。</span><span class="sxs-lookup"><span data-stu-id="ef846-149">Select the **HireInfo** entity type and click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="ef846-150">選択**Person**から、 **&lt;テーブルまたはビューの追加&gt;** フィールドのドロップダウン リスト。</span><span class="sxs-lookup"><span data-stu-id="ef846-150">Select **Person** from the **&lt;Add a Table or View&gt;** field drop-down list.</span></span> <span data-ttu-id="ef846-151">リストは、テーブルが含まれていたり、ビューを選択したエンティティをマップできます。</span><span class="sxs-lookup"><span data-stu-id="ef846-151">The list contains tables or views to which the selected entity can be mapped.</span></span>
    <span data-ttu-id="ef846-152">既定では、適切なプロパティをマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ef846-152">The appropriate properties should be mapped by default.</span></span>

    ![マップ](~/ef6/media/mapping.png)

-   <span data-ttu-id="ef846-154">選択、 **PersonHireInfo**デザイン画面でアソシエーション。</span><span class="sxs-lookup"><span data-stu-id="ef846-154">Select the **PersonHireInfo** association on the design surface.</span></span>
-   <span data-ttu-id="ef846-155">デザイン サーフェスと選択のアソシエーションを右クリックして**プロパティ**します。</span><span class="sxs-lookup"><span data-stu-id="ef846-155">Right-click the association on the design surface and select **Properties**.</span></span>
-   <span data-ttu-id="ef846-156">**プロパティ**ウィンドウで、**参照に関する制約**プロパティの省略記号ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="ef846-156">In the **Properties** window, select the **Referential Constraints** property and click the ellipses button.</span></span>
-   <span data-ttu-id="ef846-157">選択**Person**から、**プリンシパル**ドロップダウン リスト。</span><span class="sxs-lookup"><span data-stu-id="ef846-157">Select **Person** from the **Principal** drop-down list.</span></span>
-   <span data-ttu-id="ef846-158">**[OK]** を押します。</span><span class="sxs-lookup"><span data-stu-id="ef846-158">Press **OK**.</span></span>

 

## <a name="use-the-model"></a><span data-ttu-id="ef846-159">モデルを使用します。</span><span class="sxs-lookup"><span data-stu-id="ef846-159">Use the Model</span></span>

-   <span data-ttu-id="ef846-160">Main メソッドでは、次のコードを貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="ef846-160">Paste the following code in the Main method.</span></span>

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
-   <span data-ttu-id="ef846-161">アプリケーションをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="ef846-161">Compile and run the application.</span></span>

<span data-ttu-id="ef846-162">に対して次の T-SQL ステートメントが実行された、**学校**このアプリケーションを実行した結果のデータベース。</span><span class="sxs-lookup"><span data-stu-id="ef846-162">The following T-SQL statements were executed against the **School** database as a result of running this application.</span></span> 

-   <span data-ttu-id="ef846-163">次**挿入**コンテキストを実行した結果として実行されました。データを SaveChanges() と組み合わせて、 **Person**と**HireInfo**エンティティ</span><span class="sxs-lookup"><span data-stu-id="ef846-163">The following **INSERT** was executed as a result of executing context.SaveChanges() and combines data from the **Person** and **HireInfo** entities</span></span>

    ![挿入](~/ef6/media/insert.png)

-   <span data-ttu-id="ef846-165">次**選択**コンテキストを実行した結果として実行されました。マップされている列だけ選択して People.FirstOrDefault()**人**</span><span class="sxs-lookup"><span data-stu-id="ef846-165">The following **SELECT** was executed as a result of executing context.People.FirstOrDefault() and selects just the columns mapped to **Person**</span></span>

    ![1 を選択します](~/ef6/media/select1.png)

-   <span data-ttu-id="ef846-167">次**選択**ナビゲーション プロパティの existingPerson.Instructor へのアクセスの結果は実行しにマップされている列だけを選択します**HireInfo。**</span><span class="sxs-lookup"><span data-stu-id="ef846-167">The following **SELECT** was executed as a result of accessing the navigation property existingPerson.Instructor and selects just the columns mapped to **HireInfo**</span></span>

    ![2 を選択します。](~/ef6/media/select2.png)
