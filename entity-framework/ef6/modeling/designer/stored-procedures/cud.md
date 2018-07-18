---
title: デザイナーの CUD ストアド プロシージャ - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 1e773972-2da5-45e0-85a2-3cf3fbcfa5cf
caps.latest.revision: 3
ms.openlocfilehash: 6b6a1f843142713153fa86309ef55f9d6e804766
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2018
ms.locfileid: "39121931"
---
# <a name="designer-cud-stored-procedures"></a><span data-ttu-id="51908-102">デザイナーの CUD ストアド プロシージャ</span><span class="sxs-lookup"><span data-stu-id="51908-102">Designer CUD Stored Procedures</span></span>
<span data-ttu-id="51908-103">このステップ バイ ステップ チュートリアルの作成をマップする方法を表示する\\挿入、更新、および Entity Framework デザイナー (EF Designer) を使用してストアド プロシージャにエンティティ型の (CUD) 操作を削除します。</span><span class="sxs-lookup"><span data-stu-id="51908-103">This step-by-step walkthrough show how to map the create\\insert, update, and delete (CUD) operations of an entity type to stored procedures using the Entity Framework Designer (EF Designer).</span></span>  <span data-ttu-id="51908-104">既定では、Entity Framework は、CUD 操作で、SQL ステートメントを自動的に生成されますが、これらの操作をストアド プロシージャをマップすることもできます。</span><span class="sxs-lookup"><span data-stu-id="51908-104">By default, the Entity Framework automatically generates the SQL statements for the CUD operations, but you can also map stored procedures to these operations.</span></span>  

<span data-ttu-id="51908-105">Code First はサポートされていないことをストアド プロシージャまたは関数のマッピングに注意してください。</span><span class="sxs-lookup"><span data-stu-id="51908-105">Note, that Code First does not support mapping to stored procedures or functions.</span></span> <span data-ttu-id="51908-106">ただし、System.Data.Entity.DbSet.SqlQuery メソッドを使用して、ストアド プロシージャまたは関数を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="51908-106">However, you can call stored procedures or functions by using the System.Data.Entity.DbSet.SqlQuery method.</span></span> <span data-ttu-id="51908-107">例えば:</span><span class="sxs-lookup"><span data-stu-id="51908-107">For example:</span></span>
``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]");
```

## <a name="considerations-when-mapping-the-cud-operations-to-stored-procedures"></a><span data-ttu-id="51908-108">ストアド プロシージャをマップする CUD 操作時の考慮事項</span><span class="sxs-lookup"><span data-stu-id="51908-108">Considerations when Mapping the CUD Operations to Stored Procedures</span></span>

<span data-ttu-id="51908-109">CUD 操作をストアド プロシージャにマップする場合は、次の考慮事項が適用されます。</span><span class="sxs-lookup"><span data-stu-id="51908-109">When mapping the CUD operations to stored procedures, the following considerations apply:</span></span> 

-   <span data-ttu-id="51908-110">CUD 操作の 1 つは、ストアド プロシージャにマッピングは、する場合は、それらのすべてをマップします。</span><span class="sxs-lookup"><span data-stu-id="51908-110">If you are mapping one of the CUD operations to a stored procedure, map all of them.</span></span> <span data-ttu-id="51908-111">実行された場合、3 つすべてをマップしない場合、マップされていない操作が失敗**UpdateException**がスローされます。</span><span class="sxs-lookup"><span data-stu-id="51908-111">If you do not map all three, the unmapped operations will fail if executed and an **UpdateException** will be thrown.</span></span>
-   <span data-ttu-id="51908-112">ストアド プロシージャのすべてのパラメーターは、エンティティのプロパティにマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="51908-112">You must map every parameter of the stored procedure to entity properties.</span></span>
-   <span data-ttu-id="51908-113">サーバーは、挿入行の主キーの値を生成する場合は、エンティティのキー プロパティにこの値をマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="51908-113">If the server generates the primary key value for the inserted row, you must map this value back to the entity's key property.</span></span> <span data-ttu-id="51908-114">次の例では、 **InsertPerson**ストアド プロシージャは、ストアド プロシージャの結果セットの一部として新しく作成された主キーを返します。</span><span class="sxs-lookup"><span data-stu-id="51908-114">In the example that follows, the **InsertPerson** stored procedure returns the newly created primary key as part of the stored procedure's result set.</span></span> <span data-ttu-id="51908-115">主キーは、エンティティ キーにマップされます (**PersonID**) を使用して、 **&lt;結果バインドの追加&gt;** EF デザイナーの機能です。</span><span class="sxs-lookup"><span data-stu-id="51908-115">The primary key is mapped to the entity key (**PersonID**) using the **&lt;Add Result Bindings&gt;** feature of the EF Designer.</span></span>
-   <span data-ttu-id="51908-116">ストアド プロシージャの呼び出しは、概念モデルのエンティティにマップされた 1:1 です。</span><span class="sxs-lookup"><span data-stu-id="51908-116">The stored procedure calls are mapped 1:1 with the entities in the conceptual model.</span></span> <span data-ttu-id="51908-117">たとえば、概念モデルと、マップで継承階層を実装する場合、CUD はストアド プロシージャの**親**(基本)、**子**(派生) エンティティを保存、 **子**変更の呼び出しだけ、**子**ストアド プロシージャはトリガーされません、**親**のストアド プロシージャが。</span><span class="sxs-lookup"><span data-stu-id="51908-117">For example, if you implement an inheritance hierarchy in your conceptual model and then map the CUD stored procedures for the **Parent** (base) and the **Child** (derived) entities, saving the **Child** changes will only call the **Child**’s stored procedures, it will not trigger the **Parent**’s stored procedures calls.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="51908-118">前提条件</span><span class="sxs-lookup"><span data-stu-id="51908-118">Prerequisites</span></span>

<span data-ttu-id="51908-119">このチュートリアルを完了するための要件を次に示します。</span><span class="sxs-lookup"><span data-stu-id="51908-119">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="51908-120">Visual Studio の最新バージョン。</span><span class="sxs-lookup"><span data-stu-id="51908-120">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="51908-121">[School サンプル データベース](~/ef6/resources/school-database.md)します。</span><span class="sxs-lookup"><span data-stu-id="51908-121">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="51908-122">プロジェクトを設定します。</span><span class="sxs-lookup"><span data-stu-id="51908-122">Set up the Project</span></span>

-   <span data-ttu-id="51908-123">Visual Studio 2012 を開きます。</span><span class="sxs-lookup"><span data-stu-id="51908-123">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="51908-124">選択**ファイル -&gt;新機能 -&gt;プロジェクト**</span><span class="sxs-lookup"><span data-stu-id="51908-124">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="51908-125">左側のウィンドウで次のようにクリックします。 **Visual C\#** を選び、**コンソール**テンプレート。</span><span class="sxs-lookup"><span data-stu-id="51908-125">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="51908-126">入力**CUDSProcsSample**名として。</span><span class="sxs-lookup"><span data-stu-id="51908-126">Enter **CUDSProcsSample** as the name.</span></span>
-   <span data-ttu-id="51908-127">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="51908-127">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="51908-128">モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="51908-128">Create a Model</span></span>

-   <span data-ttu-id="51908-129">ソリューション エクスプ ローラーでプロジェクト名を右クリックして**追加 -&gt;新しい項目の**します。</span><span class="sxs-lookup"><span data-stu-id="51908-129">Right-click the project name in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="51908-130">選択**データ**選択し、左側のメニューから**ADO.NET Entity Data Model**テンプレート ペインでします。</span><span class="sxs-lookup"><span data-stu-id="51908-130">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="51908-131">入力**CUDSProcs.edmx**のファイル名、およびクリック**追加**します。</span><span class="sxs-lookup"><span data-stu-id="51908-131">Enter **CUDSProcs.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="51908-132">モデルのコンテンツの選択 ダイアログ ボックスで、次のように選択します。**データベースから生成**、 をクリックし、**次**。</span><span class="sxs-lookup"><span data-stu-id="51908-132">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="51908-133">クリックして**新しい接続**します。</span><span class="sxs-lookup"><span data-stu-id="51908-133">Click **New Connection**.</span></span> <span data-ttu-id="51908-134">接続のプロパティ ダイアログ ボックスで、サーバー名を入力します (たとえば、 **(localdb)\\mssqllocaldb**) を選択します、認証方式として、型**学校**データベース名、し、。クリックして**OK**します。</span><span class="sxs-lookup"><span data-stu-id="51908-134">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="51908-135">データ接続の選択 ダイアログ ボックスは、データベース接続の設定で更新されます。</span><span class="sxs-lookup"><span data-stu-id="51908-135">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="51908-136">選択 で、データベース オブジェクト ダイアログ ボックスで、**テーブル**ノードを選択、 **Person**テーブル。</span><span class="sxs-lookup"><span data-stu-id="51908-136">In the Choose Your Database Objects dialog box, under the **Tables** node, select the **Person** table.</span></span>
-   <span data-ttu-id="51908-137">また、次のストアド プロシージャの選択、**ストアド プロシージャおよび関数**ノード: **DeletePerson**、 **InsertPerson**、および**UpdatePerson**.</span><span class="sxs-lookup"><span data-stu-id="51908-137">Also, select the following stored procedures under the **Stored Procedures and Functions** node: **DeletePerson**, **InsertPerson**, and **UpdatePerson**.</span></span> 
-   <span data-ttu-id="51908-138">Visual Studio 2012 で EF Designer を起動すると、ストアド プロシージャの一括インポートがサポートしています。</span><span class="sxs-lookup"><span data-stu-id="51908-138">Starting with Visual Studio 2012 the EF Designer supports bulk import of stored procedures.</span></span> <span data-ttu-id="51908-139">**エンティティ モデルに選択されたストアド プロシージャおよび関数のインポート**が既定でオンになっています。</span><span class="sxs-lookup"><span data-stu-id="51908-139">The **Import selected stored procedures and functions into the entity model** is checked by default.</span></span> <span data-ttu-id="51908-140">この例では、挿入、更新、およびエンティティ型を削除するプロシージャを格納しますがため、私たちにインポートしたくないとは、このチェック ボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="51908-140">Since in this example we have stored procedures that insert, update, and delete entity types, we do not want to import them and will uncheck this checkbox.</span></span> 

    ![ImportSProcs](~/ef6/media/importsprocs.jpg)

-   <span data-ttu-id="51908-142">**[完了]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="51908-142">Click **Finish**.</span></span>
    <span data-ttu-id="51908-143">モデルを編集するため、デザイン サーフェイスを提供する EF デザイナーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="51908-143">The EF Designer, which provides a design surface for editing your model, is displayed.</span></span>

## <a name="map-the-person-entity-to-stored-procedures"></a><span data-ttu-id="51908-144">Person エンティティをストアド プロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="51908-144">Map the Person Entity to Stored Procedures</span></span>

-   <span data-ttu-id="51908-145">右クリックし、 **Person**エンティティの種類と選択**ストアド プロシージャ マッピング**します。</span><span class="sxs-lookup"><span data-stu-id="51908-145">Right-click the **Person** entity type and select **Stored Procedure Mapping**.</span></span>
-   <span data-ttu-id="51908-146">ストアド プロシージャのマッピングが表示されます、**マッピングの詳細**ウィンドウ。</span><span class="sxs-lookup"><span data-stu-id="51908-146">The stored procedure mappings appear in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="51908-147">クリックして**&lt;関数の挿入 をクリック&gt;** します。</span><span class="sxs-lookup"><span data-stu-id="51908-147">Click **&lt;Select Insert Function&gt;**.</span></span>
    <span data-ttu-id="51908-148">このフィールドは、概念モデルのエンティティ型にマップできる、ストレージ モデル内のストアド プロシージャが表示されるドロップダウン リストです。</span><span class="sxs-lookup"><span data-stu-id="51908-148">The field becomes a drop-down list of the stored procedures in the storage model that can be mapped to entity types in the conceptual model.</span></span>
    <span data-ttu-id="51908-149">選択**InsertPerson**ドロップダウン リストから。</span><span class="sxs-lookup"><span data-stu-id="51908-149">Select **InsertPerson** from the drop-down list.</span></span>
-   <span data-ttu-id="51908-150">ストアド プロシージャのパラメーターとエンティティのプロパティとの既定のマッピングが表示されます。</span><span class="sxs-lookup"><span data-stu-id="51908-150">Default mappings between stored procedure parameters and entity properties appear.</span></span> <span data-ttu-id="51908-151">矢印はマッピングの方向を表します (プロパティの値がストアド プロシージャのパラメーターに渡される)。</span><span class="sxs-lookup"><span data-stu-id="51908-151">Note that arrows indicate the mapping direction: Property values are supplied to stored procedure parameters.</span></span>
-   <span data-ttu-id="51908-152">クリックして**&lt;結果バインドの追加&gt;** します。</span><span class="sxs-lookup"><span data-stu-id="51908-152">Click **&lt;Add Result Binding&gt;**.</span></span>
-   <span data-ttu-id="51908-153">型**NewPersonID**、によって返されるパラメーターの名前、 **InsertPerson**ストアド プロシージャ。</span><span class="sxs-lookup"><span data-stu-id="51908-153">Type **NewPersonID**, the name of the parameter returned by the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="51908-154">先頭または末尾のスペースを入力することを確認してください。</span><span class="sxs-lookup"><span data-stu-id="51908-154">Make sure not to type leading or trailing spaces.</span></span>
-   <span data-ttu-id="51908-155">**Enter** キーを押します。</span><span class="sxs-lookup"><span data-stu-id="51908-155">Press **Enter**.</span></span>
-   <span data-ttu-id="51908-156">既定では、 **NewPersonID**エンティティ キーにマップされて**PersonID**します。</span><span class="sxs-lookup"><span data-stu-id="51908-156">By default, **NewPersonID** is mapped to the entity key **PersonID**.</span></span> <span data-ttu-id="51908-157">矢印はマッピングの方向を表します (結果列の値がプロパティに渡される)。</span><span class="sxs-lookup"><span data-stu-id="51908-157">Note that an arrow indicates the direction of the mapping: The value of the result column is supplied to the property.</span></span>

    ![MappingDetails](~/ef6/media/mappingdetails.png)

-   <span data-ttu-id="51908-159">をクリックして **&lt;Update 関数の選択&gt;** 選択**UpdatePerson**結果のドロップダウン リストから。</span><span class="sxs-lookup"><span data-stu-id="51908-159">Click **&lt;Select Update Function&gt;** and select **UpdatePerson** from the resulting drop-down list.</span></span>
-   <span data-ttu-id="51908-160">ストアド プロシージャのパラメーターとエンティティのプロパティとの既定のマッピングが表示されます。</span><span class="sxs-lookup"><span data-stu-id="51908-160">Default mappings between stored procedure parameters and entity properties appear.</span></span>
-   <span data-ttu-id="51908-161">をクリックして**&lt;削除関数の選択&gt;** 選択と**DeletePerson**結果のドロップダウン リストから。</span><span class="sxs-lookup"><span data-stu-id="51908-161">Click **&lt;Select Delete Function&gt;** and select **DeletePerson** from the resulting drop-down list.</span></span>
-   <span data-ttu-id="51908-162">ストアド プロシージャのパラメーターとエンティティのプロパティとの既定のマッピングが表示されます。</span><span class="sxs-lookup"><span data-stu-id="51908-162">Default mappings between stored procedure parameters and entity properties appear.</span></span>

<span data-ttu-id="51908-163">挿入、更新、および削除の操作、 **Person**エンティティ型がストアド プロシージャにマップされています。</span><span class="sxs-lookup"><span data-stu-id="51908-163">The insert, update, and delete operations of the **Person** entity type are now mapped to stored procedures.</span></span>

<span data-ttu-id="51908-164">同時実行更新またはストアド プロシージャを使用したエンティティの削除時のチェックを有効にする場合は、次のオプションのいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="51908-164">If you want to enable concurrency checking when updating or deleting an entity with stored procedures, use one of the following options:</span></span>

-   <span data-ttu-id="51908-165">使用して、**出力**パラメーターの数を返すには、ストアド プロシージャとチェックからの行が影響を受ける、**行数パラメーター**パラメーター名の横にあるチェック ボックスをオンします。</span><span class="sxs-lookup"><span data-stu-id="51908-165">Use an **OUTPUT** parameter to return the number of affected rows from the stored procedure and check the **Rows Affected Parameter** checkbox next to the parameter name.</span></span> <span data-ttu-id="51908-166">操作が呼び出されると、返される値が 0 の場合、 [ **OptimisticConcurrencyException** ](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx)がスローされます。</span><span class="sxs-lookup"><span data-stu-id="51908-166">If the value returned is zero when the operation is called, an  [**OptimisticConcurrencyException**](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx) will be thrown.</span></span>
-   <span data-ttu-id="51908-167">チェック、**元の値を使用して**同時実行チェックに使用するプロパティの横にあるチェック ボックスをオンします。</span><span class="sxs-lookup"><span data-stu-id="51908-167">Check the **Use Original Value** checkbox next to a property that you want to use for concurrency checking.</span></span> <span data-ttu-id="51908-168">更新しようとすると、元のデータベースにデータの書き込み時にデータベースから読み取られた元のプロパティの値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="51908-168">When an update is attempted, the value of the property that was originally read from the database will be used when writing data back to the database.</span></span> <span data-ttu-id="51908-169">値が、データベース内の値と一致しない場合、 **OptimisticConcurrencyException**がスローされます。</span><span class="sxs-lookup"><span data-stu-id="51908-169">If the value does not match the value in the database, an **OptimisticConcurrencyException** will be thrown.</span></span>

## <a name="use-the-model"></a><span data-ttu-id="51908-170">モデルを使用します。</span><span class="sxs-lookup"><span data-stu-id="51908-170">Use the Model</span></span>

<span data-ttu-id="51908-171">開く、 **Program.cs**ファイルの場所、 **Main**メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="51908-171">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="51908-172">Main 関数には、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="51908-172">Add the following code into the Main function.</span></span>

<span data-ttu-id="51908-173">コードを作成する新しい**Person**オブジェクト、オブジェクトを更新し、最後に、オブジェクトを削除します。</span><span class="sxs-lookup"><span data-stu-id="51908-173">The code creates a new **Person** object, then updates the object, and finally deletes the object.</span></span>         

``` csharp
    using (var context = new SchoolEntities())
    {
        var newInstructor = new Person
        {
            FirstName = "Robyn",
            LastName = "Martin",
            HireDate = DateTime.Now,
            Discriminator = "Instructor"
        }

        // Add the new object to the context.
        context.People.Add(newInstructor);

        Console.WriteLine("Added {0} {1} to the context.",
            newInstructor.FirstName, newInstructor.LastName);

        Console.WriteLine("Before SaveChanges, the PersonID is: {0}",
            newInstructor.PersonID);

        // SaveChanges will call the InsertPerson sproc.  
        // The PersonID property will be assigned the value
        // returned by the sproc.
        context.SaveChanges();

        Console.WriteLine("After SaveChanges, the PersonID is: {0}",
            newInstructor.PersonID);

        // Modify the object and call SaveChanges.
        // This time, the UpdatePerson will be called.
        newInstructor.FirstName = "Rachel";
        context.SaveChanges();

        // Remove the object from the context and call SaveChanges.
        // The DeletePerson sproc will be called.
        context.People.Remove(newInstructor);
        context.SaveChanges();

        Person deletedInstructor = context.People.
            Where(p => p.PersonID == newInstructor.PersonID).
            FirstOrDefault();

        if (deletedInstructor == null)
            Console.WriteLine("A person with PersonID {0} was deleted.",
                newInstructor.PersonID);
    }
```

-   <span data-ttu-id="51908-174">アプリケーションをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="51908-174">Compile and run the application.</span></span> <span data-ttu-id="51908-175">プログラムには、次の出力が生成されます \*。</span><span class="sxs-lookup"><span data-stu-id="51908-175">The program produces the following output \*</span></span>
    >[!NOTE]
> <span data-ttu-id="51908-176">PersonID、サーバーによって自動生成されたため、別の数 \* 表示ほとんどの場合には、します。</span><span class="sxs-lookup"><span data-stu-id="51908-176">PersonID is auto-generated by the server, so you will most likely see a different number\*</span></span>

```
Added Robyn Martin to the context.
Before SaveChanges, the PersonID is: 0
After SaveChanges, the PersonID is: 51
A person with PersonID 51 was deleted.
```

<span data-ttu-id="51908-177">Ultimate のバージョンの Visual Studio を使用する場合は、実行される SQL ステートメントを表示すると、デバッガーで Intellitrace を使用できます。</span><span class="sxs-lookup"><span data-stu-id="51908-177">If you are working with the Ultimate version of Visual Studio, you can use Intellitrace with the debugger to see the SQL statements that get executed.</span></span>

![Intellitrace](~/ef6/media/intellitrace.png)
