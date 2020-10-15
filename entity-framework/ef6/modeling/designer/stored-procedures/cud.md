---
title: デザイナー CUD ストアドプロシージャ-EF6
description: Entity Framework 6 のデザイナー CUD ストアドプロシージャ
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/stored-procedures/cud
ms.openlocfilehash: 167504d3abbd156f426c481c410a1953bc047de4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066123"
---
# <a name="designer-cud-stored-procedures"></a><span data-ttu-id="9a073-103">デザイナー CUD ストアドプロシージャ</span><span class="sxs-lookup"><span data-stu-id="9a073-103">Designer CUD Stored Procedures</span></span>

<span data-ttu-id="9a073-104">このステップバイステップチュートリアルでは \\ 、Entity Framework Designer (EF デザイナー) を使用して、エンティティ型の create insert、update、および delete (CUD) 操作をストアドプロシージャにマップする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="9a073-104">This step-by-step walkthrough show how to map the create\\insert, update, and delete (CUD) operations of an entity type to stored procedures using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="9a073-105">既定では、CUD 操作の SQL ステートメントが Entity Framework によって自動的に生成されますが、ストアドプロシージャをこれらの操作にマップすることもできます。</span><span class="sxs-lookup"><span data-stu-id="9a073-105"> By default, the Entity Framework automatically generates the SQL statements for the CUD operations, but you can also map stored procedures to these operations.</span></span>  

<span data-ttu-id="9a073-106">この Code First では、ストアドプロシージャまたは関数へのマッピングはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="9a073-106">Note, that Code First does not support mapping to stored procedures or functions.</span></span> <span data-ttu-id="9a073-107">ただし、ストアドプロシージャまたは関数は、ストアドプロシージャまたは関数を呼び出すことによって呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="9a073-107">However, you can call stored procedures or functions by using the System.Data.Entity.DbSet.SqlQuery method.</span></span> <span data-ttu-id="9a073-108">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="9a073-108">For example:</span></span>

``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]");
```

## <a name="considerations-when-mapping-the-cud-operations-to-stored-procedures"></a><span data-ttu-id="9a073-109">CUD 操作をストアドプロシージャにマップする際の注意点</span><span class="sxs-lookup"><span data-stu-id="9a073-109">Considerations when Mapping the CUD Operations to Stored Procedures</span></span>

<span data-ttu-id="9a073-110">CUD 操作をストアドプロシージャにマップする場合は、次の考慮事項が適用されます。</span><span class="sxs-lookup"><span data-stu-id="9a073-110">When mapping the CUD operations to stored procedures, the following considerations apply:</span></span>

- <span data-ttu-id="9a073-111">CUD 操作のいずれかをストアドプロシージャにマップする場合は、すべての操作をマップします。</span><span class="sxs-lookup"><span data-stu-id="9a073-111">If you are mapping one of the CUD operations to a stored procedure, map all of them.</span></span> <span data-ttu-id="9a073-112">これら3つをすべてマップしないと、マップされていない操作が実行され、 **Updateexception**   がスローされた場合に失敗します。</span><span class="sxs-lookup"><span data-stu-id="9a073-112">If you do not map all three, the unmapped operations will fail if executed and an **UpdateException** will be thrown.</span></span>
- <span data-ttu-id="9a073-113">ストアドプロシージャのすべてのパラメーターをエンティティプロパティにマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="9a073-113">You must map every parameter of the stored procedure to entity properties.</span></span>
- <span data-ttu-id="9a073-114">挿入された行の主キーの値をサーバーが生成する場合は、この値をエンティティのキープロパティにマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="9a073-114">If the server generates the primary key value for the inserted row, you must map this value back to the entity's key property.</span></span> <span data-ttu-id="9a073-115">次の例では、 **Insertperson**   ストアドプロシージャは、新しく作成された主キーをストアドプロシージャの結果セットの一部として返します。</span><span class="sxs-lookup"><span data-stu-id="9a073-115">In the example that follows, the **InsertPerson** stored procedure returns the newly created primary key as part of the stored procedure's result set.</span></span> <span data-ttu-id="9a073-116">プライマリキーは、EF デザイナーの [ \*\* &lt; 結果 &gt; バインドの追加**] 機能を使用してエンティティキー (**PersonID\*\*) にマップされ   ます。</span><span class="sxs-lookup"><span data-stu-id="9a073-116">The primary key is mapped to the entity key (**PersonID**) using the **&lt;Add Result Bindings&gt;** feature of the EF Designer.</span></span>
- <span data-ttu-id="9a073-117">ストアドプロシージャの呼び出しは、概念モデルのエンティティを使用して1:1 にマップされます。</span><span class="sxs-lookup"><span data-stu-id="9a073-117">The stored procedure calls are mapped 1:1 with the entities in the conceptual model.</span></span> <span data-ttu-id="9a073-118">たとえば、概念モデルに継承階層を実装し、 **親** (基本) エンティティと **子** (派生) エンティティに対して CUD ストアドプロシージャをマップした場合、 **子** の変更を保存しても、 **子**のストアドプロシージャのみが呼び出されます。この場合、 **親**のストアドプロシージャの呼び出しはトリガーされません。</span><span class="sxs-lookup"><span data-stu-id="9a073-118">For example, if you implement an inheritance hierarchy in your conceptual model and then map the CUD stored procedures for the **Parent** (base) and the **Child** (derived) entities, saving the **Child** changes will only call the **Child**’s stored procedures, it will not trigger the **Parent**’s stored procedures calls.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9a073-119">前提条件</span><span class="sxs-lookup"><span data-stu-id="9a073-119">Prerequisites</span></span>

<span data-ttu-id="9a073-120">このチュートリアルを完了するための要件を次に示します。</span><span class="sxs-lookup"><span data-stu-id="9a073-120">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="9a073-121">Visual Studio の最新バージョン。</span><span class="sxs-lookup"><span data-stu-id="9a073-121">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="9a073-122">[School サンプルデータベース](xref:ef6/resources/school-database)。</span><span class="sxs-lookup"><span data-stu-id="9a073-122">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="9a073-123">プロジェクトを設定する</span><span class="sxs-lookup"><span data-stu-id="9a073-123">Set up the Project</span></span>

- <span data-ttu-id="9a073-124">Visual Studio 2012 を開きます。</span><span class="sxs-lookup"><span data-stu-id="9a073-124">Open Visual Studio 2012.</span></span>
- <span data-ttu-id="9a073-125">**ファイルの選択- &gt; 新規作成- &gt; プロジェクト**</span><span class="sxs-lookup"><span data-stu-id="9a073-125">Select **File-&gt; New -&gt; Project**</span></span>
- <span data-ttu-id="9a073-126">左側のウィンドウで、[ **Visual C \# **] をクリックし、**コンソール**テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="9a073-126">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
- <span data-ttu-id="9a073-127">名前として「 **Cudsprocssample**」と入力し   ます。</span><span class="sxs-lookup"><span data-stu-id="9a073-127">Enter **CUDSProcsSample** as the name.</span></span>
- <span data-ttu-id="9a073-128"> **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9a073-128">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="9a073-129">モデルの作成</span><span class="sxs-lookup"><span data-stu-id="9a073-129">Create a Model</span></span>

- <span data-ttu-id="9a073-130">ソリューションエクスプローラーでプロジェクト名を右クリックし、[追加]、[ \*\* &gt; 新しい項目\*\*] の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9a073-130">Right-click the project name in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
- <span data-ttu-id="9a073-131">左側のメニューから [ **データ** ] を選択し、[テンプレート] ペインで [ **ADO.NET Entity Data Model** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="9a073-131">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
- <span data-ttu-id="9a073-132">ファイル名として「 **Cudsprocs .edmx** 」と入力し、[ **追加**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9a073-132">Enter **CUDSProcs.edmx** for the file name, and then click **Add**.</span></span>
- <span data-ttu-id="9a073-133">[モデルのコンテンツの選択] ダイアログボックスで、[ **データベースから生成**] を選択し、[ **次へ**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9a073-133">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
- <span data-ttu-id="9a073-134">[ **新しい接続**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9a073-134">Click **New Connection**.</span></span> <span data-ttu-id="9a073-135">[接続プロパティ] ダイアログボックスで、サーバー名 (たとえば、 **(localdb) \\ mssqllocaldb**) を入力し、認証方法を選択します。データベース名として「 **School**」と入力し、[    **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9a073-135">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="9a073-136">[データ接続の選択] ダイアログボックスが、データベース接続の設定によって更新されます。</span><span class="sxs-lookup"><span data-stu-id="9a073-136">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
- <span data-ttu-id="9a073-137">[データベースオブジェクトの選択] ダイアログボックスの [ **テーブル**] ノードで、[    **Person** ] テーブルを選択します。</span><span class="sxs-lookup"><span data-stu-id="9a073-137">In the Choose Your Database Objects dialog box, under the **Tables** node, select the **Person** table.</span></span>
- <span data-ttu-id="9a073-138">また、[ **ストアドプロシージャと関数** ] ノードで、[ **deleteperson**]、[ **Insertperson**]、[ **updateperson**] の各ストアドプロシージャを選択します。</span><span class="sxs-lookup"><span data-stu-id="9a073-138">Also, select the following stored procedures under the **Stored Procedures and Functions** node: **DeletePerson**, **InsertPerson**, and **UpdatePerson**.</span></span>
- <span data-ttu-id="9a073-139">Visual Studio 2012 以降では、EF デザイナーはストアドプロシージャの一括インポートをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="9a073-139">Starting with Visual Studio 2012 the EF Designer supports bulk import of stored procedures.</span></span> <span data-ttu-id="9a073-140">既定では、[ **選択したストアドプロシージャと関数をエンティティモデルにインポート** する] がオンになっています。</span><span class="sxs-lookup"><span data-stu-id="9a073-140">The **Import selected stored procedures and functions into the entity model** is checked by default.</span></span> <span data-ttu-id="9a073-141">この例では、エンティティ型の挿入、更新、および削除を行うストアドプロシージャがあります。これをインポートするのではなく、このチェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="9a073-141">Since in this example we have stored procedures that insert, update, and delete entity types, we do not want to import them and will uncheck this checkbox.</span></span>

    ![プロシージャのインポート](~/ef6/media/importsprocs.jpg)

- <span data-ttu-id="9a073-143">[ **完了**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9a073-143">Click **Finish**.</span></span>
    <span data-ttu-id="9a073-144">モデルを編集するためのデザイン画面を提供する EF デザイナーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="9a073-144">The EF Designer, which provides a design surface for editing your model, is displayed.</span></span>

## <a name="map-the-person-entity-to-stored-procedures"></a><span data-ttu-id="9a073-145">Person エンティティをストアドプロシージャにマップする</span><span class="sxs-lookup"><span data-stu-id="9a073-145">Map the Person Entity to Stored Procedures</span></span>

- <span data-ttu-id="9a073-146"> **Person**エンティティ型を右クリックし、[    **ストアドプロシージャマッピング**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="9a073-146">Right-click the **Person** entity type and select **Stored Procedure Mapping**.</span></span>
- <span data-ttu-id="9a073-147">[ **マッピングの詳細**] ウィンドウに、ストアドプロシージャのマッピングが表示され   ます。</span><span class="sxs-lookup"><span data-stu-id="9a073-147">The stored procedure mappings appear in the **Mapping Details** window.</span></span>
- <span data-ttu-id="9a073-148">[ \*\* &lt; Insert 関数 &gt; の選択] を\*\*クリックします。</span><span class="sxs-lookup"><span data-stu-id="9a073-148">Click **&lt;Select Insert Function&gt;**.</span></span>
    <span data-ttu-id="9a073-149">このフィールドは、概念モデルのエンティティ型にマップできる、ストレージ モデル内のストアド プロシージャが表示されるドロップダウン リストです。</span><span class="sxs-lookup"><span data-stu-id="9a073-149">The field becomes a drop-down list of the stored procedures in the storage model that can be mapped to entity types in the conceptual model.</span></span>
    <span data-ttu-id="9a073-150">ドロップダウンリストから [ **Insertperson**] を選択し   ます。</span><span class="sxs-lookup"><span data-stu-id="9a073-150">Select **InsertPerson** from the drop-down list.</span></span>
- <span data-ttu-id="9a073-151">ストアド プロシージャのパラメーターとエンティティのプロパティとの既定のマッピングが表示されます。</span><span class="sxs-lookup"><span data-stu-id="9a073-151">Default mappings between stored procedure parameters and entity properties appear.</span></span> <span data-ttu-id="9a073-152">矢印はマッピングの方向を表します (プロパティの値がストアド プロシージャのパラメーターに渡される)。</span><span class="sxs-lookup"><span data-stu-id="9a073-152">Note that arrows indicate the mapping direction: Property values are supplied to stored procedure parameters.</span></span>
- <span data-ttu-id="9a073-153">[ \*\* &lt; 結果バインド &gt; の追加\*\*] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9a073-153">Click **&lt;Add Result Binding&gt;**.</span></span>
- <span data-ttu-id="9a073-154"> **Insertperson**ストアドプロシージャによって返されるパラメーターの名前 **NewPersonID**を入力し   ます。</span><span class="sxs-lookup"><span data-stu-id="9a073-154">Type **NewPersonID**, the name of the parameter returned by the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="9a073-155">先頭または末尾にスペースを入力しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="9a073-155">Make sure not to type leading or trailing spaces.</span></span>
- <span data-ttu-id="9a073-156">Enter **キーを押します**。</span><span class="sxs-lookup"><span data-stu-id="9a073-156">Press **Enter**.</span></span>
- <span data-ttu-id="9a073-157">既定では、 **NewPersonID**   はエンティティキー **PersonID**にマップされます。</span><span class="sxs-lookup"><span data-stu-id="9a073-157">By default, **NewPersonID** is mapped to the entity key **PersonID**.</span></span> <span data-ttu-id="9a073-158">矢印はマッピングの方向を表します (結果列の値がプロパティに渡される)。</span><span class="sxs-lookup"><span data-stu-id="9a073-158">Note that an arrow indicates the direction of the mapping: The value of the result column is supplied to the property.</span></span>

    ![マッピングの詳細](~/ef6/media/mappingdetails.png)

- <span data-ttu-id="9a073-160">[ \*\* &lt; Update &gt; 関数の選択**] をクリックし   、結果のドロップダウンリストから [ **updateperson\*\*] を選択し   ます。</span><span class="sxs-lookup"><span data-stu-id="9a073-160">Click **&lt;Select Update Function&gt;** and select **UpdatePerson** from the resulting drop-down list.</span></span>
- <span data-ttu-id="9a073-161">ストアド プロシージャのパラメーターとエンティティのプロパティとの既定のマッピングが表示されます。</span><span class="sxs-lookup"><span data-stu-id="9a073-161">Default mappings between stored procedure parameters and entity properties appear.</span></span>
- <span data-ttu-id="9a073-162">[ \*\* &lt; 削除 &gt; 関数の選択**] をクリックし   、結果として表示されるドロップダウンリストから [ **deleteperson\*\*] を選択し   ます。</span><span class="sxs-lookup"><span data-stu-id="9a073-162">Click **&lt;Select Delete Function&gt;** and select **DeletePerson** from the resulting drop-down list.</span></span>
- <span data-ttu-id="9a073-163">ストアド プロシージャのパラメーターとエンティティのプロパティとの既定のマッピングが表示されます。</span><span class="sxs-lookup"><span data-stu-id="9a073-163">Default mappings between stored procedure parameters and entity properties appear.</span></span>

<span data-ttu-id="9a073-164"> **Person**エンティティ型の挿入、更新、および削除の各操作   がストアドプロシージャにマップされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="9a073-164">The insert, update, and delete operations of the **Person** entity type are now mapped to stored procedures.</span></span>

<span data-ttu-id="9a073-165">ストアドプロシージャを使用してエンティティを更新または削除するときに、同時実行チェックを有効にする場合は、次のいずれかのオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="9a073-165">If you want to enable concurrency checking when updating or deleting an entity with stored procedures, use one of the following options:</span></span>

- <span data-ttu-id="9a073-166">**OUTPUT**パラメーターを使用して、ストアドプロシージャから影響を受ける行の数を返し、パラメーター名の横にある [**影響を受けた行のパラメーター**   ] チェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="9a073-166">Use an **OUTPUT** parameter to return the number of affected rows from the stored procedure and check the **Rows Affected Parameter** checkbox next to the parameter name.</span></span> <span data-ttu-id="9a073-167">操作が呼び出されたときに返される値が0の場合、  [**OptimisticConcurrencyException**](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx)が   スローされます。</span><span class="sxs-lookup"><span data-stu-id="9a073-167">If the value returned is zero when the operation is called, an  [**OptimisticConcurrencyException**](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx) will be thrown.</span></span>
- <span data-ttu-id="9a073-168">同時実行チェックに使用するプロパティの横にある [ **元の値を使用** する] チェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="9a073-168">Check the **Use Original Value** checkbox next to a property that you want to use for concurrency checking.</span></span> <span data-ttu-id="9a073-169">更新が試行されると、データベースにデータを書き戻すときに、最初にデータベースから読み取られたプロパティの値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9a073-169">When an update is attempted, the value of the property that was originally read from the database will be used when writing data back to the database.</span></span> <span data-ttu-id="9a073-170">値がデータベースの値と一致しない場合は、 **OptimisticConcurrencyException**   がスローされます。</span><span class="sxs-lookup"><span data-stu-id="9a073-170">If the value does not match the value in the database, an **OptimisticConcurrencyException** will be thrown.</span></span>

## <a name="use-the-model"></a><span data-ttu-id="9a073-171">モデルを使用する</span><span class="sxs-lookup"><span data-stu-id="9a073-171">Use the Model</span></span>

<span data-ttu-id="9a073-172">**Main**メソッドが定義されている**Program.cs**ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="9a073-172">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="9a073-173">Main 関数に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="9a073-173">Add the following code into the Main function.</span></span>

<span data-ttu-id="9a073-174">このコードは、新しい **Person** オブジェクトを作成し、オブジェクトを更新して、最後にオブジェクトを削除します。</span><span class="sxs-lookup"><span data-stu-id="9a073-174">The code creates a new **Person** object, then updates the object, and finally deletes the object.</span></span>

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

- <span data-ttu-id="9a073-175">アプリケーションをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="9a073-175">Compile and run the application.</span></span> <span data-ttu-id="9a073-176">プログラムは次の出力を生成します \*</span><span class="sxs-lookup"><span data-stu-id="9a073-176">The program produces the following output \*</span></span>

> [!NOTE]
> <span data-ttu-id="9a073-177">PersonID はサーバーによって自動生成されるので、ほとんどの場合は異なる数 \* が表示されます。</span><span class="sxs-lookup"><span data-stu-id="9a073-177">PersonID is auto-generated by the server, so you will most likely see a different number\*</span></span>

``` Output
Added Robyn Martin to the context.
Before SaveChanges, the PersonID is: 0
After SaveChanges, the PersonID is: 51
A person with PersonID 51 was deleted.
```

<span data-ttu-id="9a073-178">Ultimate バージョンの Visual Studio を使用している場合は、デバッガーで Intellitrace を使用して、実行される SQL ステートメントを確認できます。</span><span class="sxs-lookup"><span data-stu-id="9a073-178">If you are working with the Ultimate version of Visual Studio, you can use Intellitrace with the debugger to see the SQL statements that get executed.</span></span>

![Intellitrace を使用したデバッグ](~/ef6/media/intellitrace.png)
