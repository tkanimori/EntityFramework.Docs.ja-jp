---
title: デザイナークエリストアドプロシージャ-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9554ed25-c5c1-43be-acad-5da37739697f
ms.openlocfilehash: 2e0092b526278597e8477d47eeb642598647bb91
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182477"
---
# <a name="designer-query-stored-procedures"></a><span data-ttu-id="070c8-102">デザイナークエリのストアドプロシージャ</span><span class="sxs-lookup"><span data-stu-id="070c8-102">Designer Query Stored Procedures</span></span>
<span data-ttu-id="070c8-103">このステップバイステップチュートリアルでは、Entity Framework Designer (EF デザイナー) を使用してストアドプロシージャをモデルにインポートし、インポートされたストアドプロシージャを呼び出して結果を取得する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="070c8-103">This step-by-step walkthrough show how to use the Entity Framework Designer (EF Designer) to import stored procedures into a model and then call the imported stored procedures to retrieve results.</span></span> 

<span data-ttu-id="070c8-104">この Code First では、ストアドプロシージャまたは関数へのマッピングはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="070c8-104">Note, that Code First does not support mapping to stored procedures or functions.</span></span> <span data-ttu-id="070c8-105">ただし、ストアドプロシージャまたは関数は、ストアドプロシージャまたは関数を呼び出すことによって呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="070c8-105">However, you can call stored procedures or functions by using the System.Data.Entity.DbSet.SqlQuery method.</span></span> <span data-ttu-id="070c8-106">例 :</span><span class="sxs-lookup"><span data-stu-id="070c8-106">For example:</span></span>
``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]")`;
```

## <a name="prerequisites"></a><span data-ttu-id="070c8-107">前提条件</span><span class="sxs-lookup"><span data-stu-id="070c8-107">Prerequisites</span></span>

<span data-ttu-id="070c8-108">このチュートリアルを完了するための要件を次に示します。</span><span class="sxs-lookup"><span data-stu-id="070c8-108">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="070c8-109">Visual Studio の最新バージョン。</span><span class="sxs-lookup"><span data-stu-id="070c8-109">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="070c8-110">[School サンプルデータベース](~/ef6/resources/school-database.md)。</span><span class="sxs-lookup"><span data-stu-id="070c8-110">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="070c8-111">プロジェクトを設定する</span><span class="sxs-lookup"><span data-stu-id="070c8-111">Set up the Project</span></span>

-   <span data-ttu-id="070c8-112">Visual Studio 2012 を開きます。</span><span class="sxs-lookup"><span data-stu-id="070c8-112">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="070c8-113">[**ファイル]、[新規&gt; プロジェクトの&gt;** ] の選択</span><span class="sxs-lookup"><span data-stu-id="070c8-113">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="070c8-114">左側のウィンドウで、[ **Visual C\#** ] をクリックし、**コンソール**テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="070c8-114">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="070c8-115">名前として「 **Efwithsprocssample** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="070c8-115">Enter **EFwithSProcsSample** as the name.</span></span>
-   <span data-ttu-id="070c8-116">[ **OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="070c8-116">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="070c8-117">モデルの作成</span><span class="sxs-lookup"><span data-stu-id="070c8-117">Create a Model</span></span>

-   <span data-ttu-id="070c8-118">ソリューションエクスプローラーでプロジェクトを右クリックし、[**追加]&gt; [新しい項目**] の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="070c8-118">Right-click the project in Solution Explorer and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="070c8-119">左側のメニューから **[データ]** を選択し、テンプレート ペインで  **[ADO.NET Entity Data Model]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="070c8-119">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="070c8-120">ファイル名として「 **Efwithsprocsmodel. .edmx** 」と入力し、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="070c8-120">Enter **EFwithSProcsModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="070c8-121">[モデルのコンテンツの選択] ダイアログボックスで、[ **データベースから生成**] を選択し、[ **次へ**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="070c8-121">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="070c8-122">[ **新しい接続**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="070c8-122">Click **New Connection**.</span></span>  
    <span data-ttu-id="070c8-123">[接続プロパティ] ダイアログボックスで、サーバー名 (たとえば、 **(localdb)\\mssqllocaldb**) を入力し、認証方法を選択します。データベース名として「 **School** 」と入力し、[ **OK**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="070c8-123">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>  
    <span data-ttu-id="070c8-124">[データ接続の選択] ダイアログボックスが、データベース接続の設定によって更新されます。</span><span class="sxs-lookup"><span data-stu-id="070c8-124">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="070c8-125">[データベースオブジェクトの選択] ダイアログボックスで、[**テーブル** ] チェックボックスをオンにして、すべてのテーブルを選択します。</span><span class="sxs-lookup"><span data-stu-id="070c8-125">In the Choose Your Database Objects dialog box, check the **Tables** checkbox to select all the tables.</span></span>  
    <span data-ttu-id="070c8-126">また、 **[ストアドプロシージャと関数]** ノードで、 **GetStudentGrades**および**GetDepartmentName**の下にある次のストアドプロシージャを選択します。</span><span class="sxs-lookup"><span data-stu-id="070c8-126">Also, select the following stored procedures under the **Stored Procedures and Functions** node: **GetStudentGrades** and **GetDepartmentName**.</span></span> 

    ![インポート](~/ef6/media/import.jpg)

    <span data-ttu-id="070c8-128">*Visual Studio 2012 以降では、EF デザイナーはストアドプロシージャの一括インポートをサポートしています。既定では、[**選択したストアドプロシージャと関数をエンティティモデルにインポート**する] がオンになっています。*</span><span class="sxs-lookup"><span data-stu-id="070c8-128">*Starting with Visual Studio 2012 the EF Designer supports bulk import of stored procedures. The **Import selected stored procedures and functions into theentity model** is checked by default.*</span></span>
-   <span data-ttu-id="070c8-129">[ **完了**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="070c8-129">Click **Finish**.</span></span>

<span data-ttu-id="070c8-130">既定では、インポートされた各ストアドプロシージャまたは複数の列を返す関数の結果構造は、自動的に新しい複合型になります。</span><span class="sxs-lookup"><span data-stu-id="070c8-130">By default, the result shape of each imported stored procedure or function that returns more than one column will automatically become a new complex type.</span></span> <span data-ttu-id="070c8-131">この例では、 **GetStudentGrades**関数の結果を **StudentGrade**エンティティにマップし、 **GetDepartmentName**の結果を**none** (既定値は**none** ) にマップします。</span><span class="sxs-lookup"><span data-stu-id="070c8-131">In this example we want to map the results of the **GetStudentGrades** function to the **StudentGrade** entity and the results of the **GetDepartmentName** to **none** (**none** is the default value).</span></span>

<span data-ttu-id="070c8-132">関数インポートでエンティティ型を返すには、対応するストアドプロシージャによって返される列が、返されたエンティティ型のスカラープロパティと正確に一致している必要があります。</span><span class="sxs-lookup"><span data-stu-id="070c8-132">For a function import to return an entity type, the columns returned by the corresponding stored procedure must exactly match the scalar properties of the returned entity type.</span></span> <span data-ttu-id="070c8-133">関数インポートは、単純型のコレクション、複合型、または値を返さないこともできます。</span><span class="sxs-lookup"><span data-stu-id="070c8-133">A function import can also return collections of simple types, complex types, or no value.</span></span>

-   <span data-ttu-id="070c8-134">デザイン画面を右クリックし、[ **モデルブラウザー**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="070c8-134">Right-click the design surface and select **Model Browser**.</span></span>
-   <span data-ttu-id="070c8-135">**モデルブラウザー**で、[ **関数インポート**] を選択し、 **GetStudentGrades**関数をダブルクリックします。</span><span class="sxs-lookup"><span data-stu-id="070c8-135">In **Model Browser**, select **Function Imports**, and then double-click the **GetStudentGrades** function.</span></span>
-   <span data-ttu-id="070c8-136">関数インポートの編集 ダイアログボックスで、 **エンティティ** を選択し、 **StudentGrade** を選択します。</span><span class="sxs-lookup"><span data-stu-id="070c8-136">In the Edit Function Import dialog box, select **Entities** and choose **StudentGrade**.</span></span>  
    <span data-ttu-id="070c8-137">***[関数]** インポート ダイアログボックスの上部にある **[関数インポートはコンポーザブル]** チェックボックスをオンにすると、コンポーザブルな関数にマップできます。このチェックボックスをオンにすると、 **[ストアドプロシージャ/関数名]** ボックスの一覧に、コンポーザブルな関数 (テーブル値関数) のみが表示されます。このチェックボックスをオンにしない場合、一覧には、コンポーザブルではない関数のみが表示されます。*</span><span class="sxs-lookup"><span data-stu-id="070c8-137">*The **Function Import is composable** checkbox at the top of the **Function Imports** dialog will let you map to composable functions. If you do check this box, only composable functions (Table-valued Functions) will appear in the **Stored Procedure / Function Name** drop-down list. If you do not check this box, only non-composable functions will be shown in the list.*</span></span>

## <a name="use-the-model"></a><span data-ttu-id="070c8-138">モデルを使用する</span><span class="sxs-lookup"><span data-stu-id="070c8-138">Use the Model</span></span>

<span data-ttu-id="070c8-139">**Main**メソッドが定義されている**Program.cs**ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="070c8-139">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="070c8-140">Main 関数に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="070c8-140">Add the following code into the Main function.</span></span>

<span data-ttu-id="070c8-141">このコードは2つのストアドプロシージャを呼び出します。 **GetStudentGrades** (指定された*StudentId*の**StudentGrades**を返します) と**GetDepartmentName** (出力パラメーターで department の名前を返します) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="070c8-141">The code calls two stored procedures: **GetStudentGrades** (returns **StudentGrades** for the specified *StudentId*) and **GetDepartmentName** (returns the name of the department in the output parameter).</span></span>  

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

<span data-ttu-id="070c8-142">アプリケーションをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="070c8-142">Compile and run the application.</span></span> <span data-ttu-id="070c8-143">このプログラムの出力は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="070c8-143">The program produces the following output:</span></span>

```console
StudentID: 2
Student grade: 4.00
StudentID: 2
Student grade: 3.50
The department name is Engineering
```

<a name="output-parameters"></a><span data-ttu-id="070c8-144">出力パラメーター</span><span class="sxs-lookup"><span data-stu-id="070c8-144">Output Parameters</span></span>
-----------------

<span data-ttu-id="070c8-145">出力パラメーターが使用されている場合、結果が完全に読み取られるまで、値は使用できません。</span><span class="sxs-lookup"><span data-stu-id="070c8-145">If output parameters are used, their values will not be available until the results have been read completely.</span></span> <span data-ttu-id="070c8-146">これは、DbDataReader の基になる動作に起因します。詳細については、「 [DataReader を使用したデータの取得](https://go.microsoft.com/fwlink/?LinkID=398589)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="070c8-146">This is due to the underlying behavior of DbDataReader, see [Retrieving Data Using a DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) for more details.</span></span>
