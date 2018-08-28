---
title: デザイナーのクエリのストアド プロシージャ - EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 9554ed25-c5c1-43be-acad-5da37739697f
ms.openlocfilehash: 29b7745c2229ce4a38ad81e11406474424adfa24
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994973"
---
# <a name="designer-query-stored-procedures"></a><span data-ttu-id="aedbd-102">デザイナーのクエリのストアド プロシージャ</span><span class="sxs-lookup"><span data-stu-id="aedbd-102">Designer Query Stored Procedures</span></span>
<span data-ttu-id="aedbd-103">このステップ バイ ステップ チュートリアルでは、モデルにストアド プロシージャをインポートする Entity Framework デザイナー (EF Designer) を使用し、結果を取得する場合は、インポートされたストアド プロシージャを呼び出す方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="aedbd-103">This step-by-step walkthrough show how to use the Entity Framework Designer (EF Designer) to import stored procedures into a model and then call the imported stored procedures to retrieve results.</span></span> 

<span data-ttu-id="aedbd-104">Code First はサポートされていないことをストアド プロシージャまたは関数のマッピングに注意してください。</span><span class="sxs-lookup"><span data-stu-id="aedbd-104">Note, that Code First does not support mapping to stored procedures or functions.</span></span> <span data-ttu-id="aedbd-105">ただし、System.Data.Entity.DbSet.SqlQuery メソッドを使用して、ストアド プロシージャまたは関数を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="aedbd-105">However, you can call stored procedures or functions by using the System.Data.Entity.DbSet.SqlQuery method.</span></span> <span data-ttu-id="aedbd-106">例えば:</span><span class="sxs-lookup"><span data-stu-id="aedbd-106">For example:</span></span>
``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]")`;
```

## <a name="prerequisites"></a><span data-ttu-id="aedbd-107">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="aedbd-107">Prerequisites</span></span>

<span data-ttu-id="aedbd-108">このチュートリアルを完了するための要件を次に示します。</span><span class="sxs-lookup"><span data-stu-id="aedbd-108">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="aedbd-109">Visual Studio の最新バージョン。</span><span class="sxs-lookup"><span data-stu-id="aedbd-109">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="aedbd-110">[School サンプル データベース](~/ef6/resources/school-database.md)します。</span><span class="sxs-lookup"><span data-stu-id="aedbd-110">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="aedbd-111">プロジェクトを設定します。</span><span class="sxs-lookup"><span data-stu-id="aedbd-111">Set up the Project</span></span>

-   <span data-ttu-id="aedbd-112">Visual Studio 2012 を開きます。</span><span class="sxs-lookup"><span data-stu-id="aedbd-112">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="aedbd-113">選択**ファイル -&gt;新機能 -&gt;プロジェクト**</span><span class="sxs-lookup"><span data-stu-id="aedbd-113">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="aedbd-114">左側のウィンドウで次のようにクリックします。 **Visual C\#** を選び、**コンソール**テンプレート。</span><span class="sxs-lookup"><span data-stu-id="aedbd-114">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="aedbd-115">入力**EFwithSProcsSample**名として。</span><span class="sxs-lookup"><span data-stu-id="aedbd-115">Enter **EFwithSProcsSample** as the name.</span></span>
-   <span data-ttu-id="aedbd-116">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="aedbd-116">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="aedbd-117">モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="aedbd-117">Create a Model</span></span>

-   <span data-ttu-id="aedbd-118">ソリューション エクスプ ローラーでプロジェクトを右クリックして**追加 -&gt;新しい項目の**します。</span><span class="sxs-lookup"><span data-stu-id="aedbd-118">Right-click the project in Solution Explorer and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="aedbd-119">選択**データ**選択し、左側のメニューから**ADO.NET Entity Data Model**テンプレート ペインでします。</span><span class="sxs-lookup"><span data-stu-id="aedbd-119">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="aedbd-120">入力**EFwithSProcsModel.edmx**のファイル名、およびクリック**追加**します。</span><span class="sxs-lookup"><span data-stu-id="aedbd-120">Enter **EFwithSProcsModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="aedbd-121">モデルのコンテンツの選択 ダイアログ ボックスで、次のように選択します。**データベースから生成**、 をクリックし、**次**。</span><span class="sxs-lookup"><span data-stu-id="aedbd-121">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="aedbd-122">クリックして**新しい接続**します。</span><span class="sxs-lookup"><span data-stu-id="aedbd-122">Click **New Connection**.</span></span>  
    <span data-ttu-id="aedbd-123">接続のプロパティ ダイアログ ボックスで、サーバー名を入力します (たとえば、 **(localdb)\\mssqllocaldb**) を選択します、認証方式として、型**学校**データベース名、し、。クリックして**OK**します。</span><span class="sxs-lookup"><span data-stu-id="aedbd-123">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>  
    <span data-ttu-id="aedbd-124">データ接続の選択 ダイアログ ボックスは、データベース接続の設定で更新されます。</span><span class="sxs-lookup"><span data-stu-id="aedbd-124">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="aedbd-125">データベース オブジェクトの選択 ダイアログ ボックスで、確認、**テーブル** チェック ボックスをすべてのテーブルを選択します。</span><span class="sxs-lookup"><span data-stu-id="aedbd-125">In the Choose Your Database Objects dialog box, check the **Tables** checkbox to select all the tables.</span></span>  
    <span data-ttu-id="aedbd-126">また、次のストアド プロシージャの選択、**ストアド プロシージャおよび関数**ノード: **GetStudentGrades**と**GetDepartmentName**します。</span><span class="sxs-lookup"><span data-stu-id="aedbd-126">Also, select the following stored procedures under the **Stored Procedures and Functions** node: **GetStudentGrades** and **GetDepartmentName**.</span></span> 

    ![インポート](~/ef6/media/import.jpg)

    <span data-ttu-id="aedbd-128">*Visual Studio 2012 で EF Designer を起動すると、ストアド プロシージャの一括インポートがサポートしています。**エンティ モデルに選択されたストアド プロシージャおよび関数のインポート**が既定でオンになっています。*</span><span class="sxs-lookup"><span data-stu-id="aedbd-128">*Starting with Visual Studio 2012 the EF Designer supports bulk import of stored procedures. The **Import selected stored procedures and functions into theentity model** is checked by default.*</span></span>
-   <span data-ttu-id="aedbd-129">**[完了]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="aedbd-129">Click **Finish**.</span></span>

<span data-ttu-id="aedbd-130">既定では、各インポートされたストアド プロシージャまたは複数の列を返す関数の結果形状の新しい複合型に自動的になります。</span><span class="sxs-lookup"><span data-stu-id="aedbd-130">By default, the result shape of each imported stored procedure or function that returns more than one column will automatically become a new complex type.</span></span> <span data-ttu-id="aedbd-131">結果をマップするこの例では、 **GetStudentGrades**関数を**StudentGrade**エンティティとの結果、 **GetDepartmentName** に**none** (**none**は既定値です)。</span><span class="sxs-lookup"><span data-stu-id="aedbd-131">In this example we want to map the results of the **GetStudentGrades** function to the **StudentGrade** entity and the results of the **GetDepartmentName** to **none** (**none** is the default value).</span></span>

<span data-ttu-id="aedbd-132">エンティティ型を返す関数インポート、対応するストアド プロシージャによって返される列は、返されるエンティティ型のスカラー プロパティを正確に一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="aedbd-132">For a function import to return an entity type, the columns returned by the corresponding stored procedure must exactly match the scalar properties of the returned entity type.</span></span> <span data-ttu-id="aedbd-133">関数インポートでは、単純型、複合型、または値のコレクションを返すこともできます。</span><span class="sxs-lookup"><span data-stu-id="aedbd-133">A function import can also return collections of simple types, complex types, or no value.</span></span>

-   <span data-ttu-id="aedbd-134">デザイン サーフェスと選択を右クリックして**モデル ブラウザー**します。</span><span class="sxs-lookup"><span data-stu-id="aedbd-134">Right-click the design surface and select **Model Browser**.</span></span>
-   <span data-ttu-id="aedbd-135">**モデル ブラウザー**を選択します**関数インポート**、し、ダブルクリック、 **GetStudentGrades**関数。</span><span class="sxs-lookup"><span data-stu-id="aedbd-135">In **Model Browser**, select **Function Imports**, and then double-click the **GetStudentGrades** function.</span></span>
-   <span data-ttu-id="aedbd-136">関数インポートの編集] ダイアログ ボックスで、[**エンティティ**選択**StudentGrade**します。</span><span class="sxs-lookup"><span data-stu-id="aedbd-136">In the Edit Function Import dialog box, select **Entities** and choose **StudentGrade**.</span></span>  
    <span data-ttu-id="aedbd-137">***関数インポートがコンポーザブル**の上部にあるチェック ボックスをオン、**関数インポート**ダイアログには、構成可能な関数にマップすることができます。このボックスをオンにしないでくださいコンポーザブルな関数 (テーブル値関数) のみがで表示されます、**ストアド プロシージャ/関数名**ドロップダウン リスト。このボックスをオンにしない場合、非コンポーザブル関数のみが一覧に表示されます。*</span><span class="sxs-lookup"><span data-stu-id="aedbd-137">*The **Function Import is composable** checkbox at the top of the **Function Imports** dialog will let you map to composable functions. If you do check this box, only composable functions (Table-valued Functions) will appear in the **Stored Procedure / Function Name** drop-down list. If you do not check this box, only non-composable functions will be shown in the list.*</span></span>

## <a name="use-the-model"></a><span data-ttu-id="aedbd-138">モデルを使用します。</span><span class="sxs-lookup"><span data-stu-id="aedbd-138">Use the Model</span></span>

<span data-ttu-id="aedbd-139">開く、 **Program.cs**ファイルの場所、 **Main**メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="aedbd-139">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="aedbd-140">Main 関数には、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="aedbd-140">Add the following code into the Main function.</span></span>

<span data-ttu-id="aedbd-141">コードは、2 つのストアド プロシージャを呼び出します: **GetStudentGrades** (返します**StudentGrades** 、指定された*StudentId*) と**GetDepartmentName**(出力パラメーターで部署の名前を返します。)</span><span class="sxs-lookup"><span data-stu-id="aedbd-141">The code calls two stored procedures: **GetStudentGrades** (returns **StudentGrades** for the specified *StudentId*) and **GetDepartmentName** (returns the name of the department in the output parameter).</span></span>  

``` csharp
    using (SchoolEntities context = new SchoolEntities())
    {
        // Specify the Student ID.
        int studentId = 2;

        // Call GetStudentGrades and iterate through the returned collection.
        foreach (StudentGrade grade in context.GetStudentGrades(studentId))
        {
            Console.WriteLine("StudentID: {0}\tSubject={1}", studentId, grade.Subject);
            Console.WriteLine("Student grade: " + grade.Grade);
        }

        // Call GetDepartmentName.
        // Declare the name variable that will contain the value returned by the output parameter.
        ObjectParameter name = new ObjectParameter("Name", typeof(String));
        context.GetDepartmentName(1, name);
        Console.WriteLine("The department name is {0}", name.Value);

    }
```

<span data-ttu-id="aedbd-142">アプリケーションをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="aedbd-142">Compile and run the application.</span></span> <span data-ttu-id="aedbd-143">このプログラムの出力は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="aedbd-143">The program produces the following output:</span></span>

```
StudentID: 2
Student grade: 4.00
StudentID: 2
Student grade: 3.50
The department name is Engineering
```

<a name="output-parameters"></a><span data-ttu-id="aedbd-144">出力パラメーター</span><span class="sxs-lookup"><span data-stu-id="aedbd-144">Output Parameters</span></span>
-----------------

<span data-ttu-id="aedbd-145">出力パラメーターを使用している場合は、結果が完全に読み取られるまでに、その値は使用できません。</span><span class="sxs-lookup"><span data-stu-id="aedbd-145">If output parameters are used, their values will not be available until the results have been read completely.</span></span> <span data-ttu-id="aedbd-146">これは、DbDataReader の基になる動作によるものを参照してください[DataReader によるデータの取得](http://go.microsoft.com/fwlink/?LinkID=398589)の詳細。</span><span class="sxs-lookup"><span data-stu-id="aedbd-146">This is due to the underlying behavior of DbDataReader, see [Retrieving Data Using a DataReader](http://go.microsoft.com/fwlink/?LinkID=398589) for more details.</span></span>
