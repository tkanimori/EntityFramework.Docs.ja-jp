---
title: デザイナー TPT 継承-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: efc78c31-b4ea-4ea3-a0cd-c69eb507020e
ms.openlocfilehash: 84330fba4807620aa242a70cd8ac76a60284416d
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415047"
---
# <a name="designer-tpt-inheritance"></a><span data-ttu-id="e4ea4-102">デザイナーの TPT 継承</span><span class="sxs-lookup"><span data-stu-id="e4ea4-102">Designer TPT Inheritance</span></span>
<span data-ttu-id="e4ea4-103">このステップバイステップチュートリアルでは、Entity Framework Designer (EF デザイナー) を使用してモデルにテーブル単位 (TPT) の継承を実装する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-103">This step-by-step walkthrough shows how to implement table-per-type (TPT) inheritance in your model using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="e4ea4-104">Table-Per-Type 継承は、データベース内の別個のテーブルを使用して、非継承プロパティと継承階層のそれぞれの型のキー プロパティのデータを維持します。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-104">Table-per-type inheritance uses a separate table in the database to maintain data for non-inherited properties and key properties for each type in the inheritance hierarchy.</span></span>

<span data-ttu-id="e4ea4-105">このチュートリアルでは、**コース**(基本型)、 **OnlineCourse** (コースから派生)、および  **OnsiteCourse** ( **course**から派生) エンティティを、同じ名前のテーブルにマップします。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-105">In this walkthrough we will map the **Course** (base type), **OnlineCourse** (derives from Course), and **OnsiteCourse** (derives from **Course**) entities to tables with the same names.</span></span> <span data-ttu-id="e4ea4-106">データベースからモデルを作成し、TPT 継承を実装するようにモデルを変更します。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-106">We'll create a model from the database and then alter the model to implement the TPT inheritance.</span></span>

<span data-ttu-id="e4ea4-107">Model First から開始してから、モデルからデータベースを生成することもできます。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-107">You can also start with the Model First and then generate the database from the model.</span></span> <span data-ttu-id="e4ea4-108">EF デザイナーでは、既定で TPT 戦略が使用されるため、モデル内のすべての継承が個別のテーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-108">The EF Designer uses the TPT strategy by default and so any inheritance in the model will be mapped to separate tables.</span></span>

## <a name="other-inheritance-options"></a><span data-ttu-id="e4ea4-109">その他の継承オプション</span><span class="sxs-lookup"><span data-stu-id="e4ea4-109">Other Inheritance Options</span></span>

<span data-ttu-id="e4ea4-110">階層ごとのテーブル (TPH) は、継承階層内のすべてのエンティティ型のデータを保持するために1つのデータベーステーブルが使用されるもう1つの種類の継承です。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-110">Table-per-Hierarchy (TPH) is another type of inheritance in which one database table is used to maintain data for all of the entity types in an inheritance hierarchy.</span></span><span data-ttu-id="e4ea4-111">  Entity Designer によるテーブルごとの継承のマッピング方法の詳細については、「 [EF DESIGNER TPH の継承](~/ef6/modeling/designer/inheritance/tph.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-111">  For information about how to map Table-per-Hierarchy inheritance with the Entity Designer, see [EF Designer TPH Inheritance](~/ef6/modeling/designer/inheritance/tph.md).</span></span> 

<span data-ttu-id="e4ea4-112">具象型継承 (TPC) と混合継承モデルは、Entity Framework ランタイムによってサポートされていますが、EF デザイナーではサポートされていないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-112">Note that, the Table-per-Concrete Type Inheritance (TPC) and mixed inheritance models are supported by the Entity Framework runtime but are not supported by the EF Designer.</span></span> <span data-ttu-id="e4ea4-113">TPC または混合継承を使用する場合は、Code First を使用する方法と、EDMX ファイルを手動で編集する方法の2つのオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-113">If you want to use TPC or mixed inheritance, you have two options: use Code First, or manually edit the EDMX file.</span></span> <span data-ttu-id="e4ea4-114">EDMX ファイルを使用することを選択した場合、[マッピングの詳細] ウィンドウは "セーフモード" になり、デザイナーを使用してマッピングを変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-114">If you choose to work with the EDMX file, the Mapping Details Window will be put into “safe mode” and you will not be able to use the designer to change the mappings.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e4ea4-115">前提条件</span><span class="sxs-lookup"><span data-stu-id="e4ea4-115">Prerequisites</span></span>

<span data-ttu-id="e4ea4-116">このチュートリアルを完了するための要件を次に示します。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-116">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="e4ea4-117">Visual Studio の最新バージョン。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-117">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="e4ea4-118">[School サンプルデータベース](~/ef6/resources/school-database.md)。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-118">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="e4ea4-119">プロジェクトを設定する</span><span class="sxs-lookup"><span data-stu-id="e4ea4-119">Set up the Project</span></span>

-   <span data-ttu-id="e4ea4-120">Visual Studio 2012 を開きます。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-120">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="e4ea4-121">[**ファイル]、[新規&gt; プロジェクトの&gt;** ] の選択</span><span class="sxs-lookup"><span data-stu-id="e4ea4-121">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="e4ea4-122">左側のウィンドウで、[ **Visual C\#** ] をクリックし、**コンソール**テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-122">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="e4ea4-123">名前として「 **Tptdbfirstsample** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-123">Enter **TPTDBFirstSample** as the name.</span></span>
-   <span data-ttu-id="e4ea4-124"> **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-124">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="e4ea4-125">モデルの作成</span><span class="sxs-lookup"><span data-stu-id="e4ea4-125">Create a Model</span></span>

-   <span data-ttu-id="e4ea4-126">ソリューションエクスプローラーでプロジェクトを右クリックし、[**追加]&gt; [新しい項目**] の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-126">Right-click the project in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="e4ea4-127">左側のメニューから **[データ]** を選択し、テンプレート ペインで  **[ADO.NET Entity Data Model]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-127">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="e4ea4-128">ファイル名として「 **Tptmodel. .edmx** 」と入力し、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-128">Enter **TPTModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="e4ea4-129">[モデルのコンテンツの選択] ダイアログボックスで、[**データベースから生成 ** ] を選択し、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-129">In the Choose Model Contents dialog box, select** Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="e4ea4-130">[ **新しい接続**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-130">Click **New Connection**.</span></span>
    <span data-ttu-id="e4ea4-131">[接続プロパティ] ダイアログボックスで、サーバー名 (たとえば、 **(localdb)\\mssqllocaldb**) を入力し、認証方法を選択します。データベース名として「 **School** 」と入力し、[ **OK**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-131">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="e4ea4-132">[データ接続の選択] ダイアログボックスが、データベース接続の設定によって更新されます。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-132">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="e4ea4-133">[データベースオブジェクトの選択] ダイアログボックスの [テーブル] ノードで、 **Department**、 **Course、OnlineCourse、および OnsiteCourse**の各テーブルを選択します。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-133">In the Choose Your Database Objects dialog box, under the Tables node, select the **Department**, **Course, OnlineCourse, and OnsiteCourse** tables.</span></span>
-   <span data-ttu-id="e4ea4-134">[ **完了**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-134">Click **Finish**.</span></span>

<span data-ttu-id="e4ea4-135">モデルを編集するためのデザイン画面を提供する Entity Designer が表示されます。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-135">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="e4ea4-136">[データベースオブジェクトの選択] ダイアログボックスで選択したすべてのオブジェクトがモデルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-136">All the objects that you selected in the Choose Your Database Objects dialog box are added to the model.</span></span>

## <a name="implement-table-per-type-inheritance"></a><span data-ttu-id="e4ea4-137">型ごとのテーブル継承の実装</span><span class="sxs-lookup"><span data-stu-id="e4ea4-137">Implement Table-per-Type Inheritance</span></span>

-   <span data-ttu-id="e4ea4-138">デザイン画面で、 **OnlineCourse**エンティティ型を右クリックし、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-138">On the design surface, right-click the **OnlineCourse** entity type and select **Properties**.</span></span>
-   <span data-ttu-id="e4ea4-139">**プロパティ** ウィンドウで、基本データ型 プロパティを「 **Course**」に設定します。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-139">In the **Properties** window, set the Base Type property to **Course**.</span></span>
-   <span data-ttu-id="e4ea4-140">**OnsiteCourse**エンティティ型を右クリックし、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-140">Right-click the **OnsiteCourse** entity type and select **Properties**.</span></span>
-   <span data-ttu-id="e4ea4-141">**プロパティ** ウィンドウで、基本データ型 プロパティを「 **Course**」に設定します。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-141">In the **Properties** window, set the Base Type property to **Course**.</span></span>
-   <span data-ttu-id="e4ea4-142">**OnlineCourse**エンティティ型と**Course**エンティティ型の間のアソシエーション (線) を右クリックします。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-142">Right-click the association (the line) between the **OnlineCourse** and **Course** entity types.</span></span>
    <span data-ttu-id="e4ea4-143">**[モデルから削除]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-143">Select **Delete from Model**.</span></span>
-   <span data-ttu-id="e4ea4-144">**OnsiteCourse**エンティティ型と**Course**エンティティ型の間のアソシエーションを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-144">Right-click the association between the **OnsiteCourse** and **Course** entity types.</span></span>
    <span data-ttu-id="e4ea4-145">**[モデルから削除]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-145">Select **Delete from Model**.</span></span>

<span data-ttu-id="e4ea4-146">ここでは、 **OnlineCourse**と**OnsiteCourse**から**CourseID**プロパティを削除します。これらのクラスは、 **Course**基本型から**CourseID**を継承するためです。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-146">We will now delete the **CourseID** property from **OnlineCourse** and **OnsiteCourse** because these classes inherit **CourseID** from the **Course** base type.</span></span>

-   <span data-ttu-id="e4ea4-147">**OnlineCourse**エンティティ型の**CourseID**プロパティを右クリックし、 **[モデルから削除]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-147">Right-click the **CourseID** property of the **OnlineCourse** entity type, and then select **Delete from Model**.</span></span>
-   <span data-ttu-id="e4ea4-148">**OnsiteCourse**エンティティ型の**CourseID**プロパティを右クリックし、 **[モデルから削除]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-148">Right-click the **CourseID** property of the **OnsiteCourse** entity type, and then select **Delete from Model**</span></span>
-   <span data-ttu-id="e4ea4-149">これで、Table-Per-Type 継承が実装されました。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-149">Table-per-type inheritance is now implemented.</span></span>

![TPT](~/ef6/media/tpt.png)

## <a name="use-the-model"></a><span data-ttu-id="e4ea4-151">モデルを使用する</span><span class="sxs-lookup"><span data-stu-id="e4ea4-151">Use the Model</span></span>

<span data-ttu-id="e4ea4-152">**Main**メソッドが定義されている**Program.cs**ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-152">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="e4ea4-153">**Main**関数に次のコードを貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-153">Paste the following code into the **Main** function.</span></span> <span data-ttu-id="e4ea4-154">このコードは、3つのクエリを実行します。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-154">The code executes three queries.</span></span> <span data-ttu-id="e4ea4-155">最初のクエリでは、指定された部門に関連するすべての**コース**が返されます。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-155">The first query brings back all **Courses** related to the specified department.</span></span> <span data-ttu-id="e4ea4-156">2番目のクエリでは、 **OfType**メソッドを使用して、指定された department に関連する**OnlineCourses**を返します。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-156">The second query uses the **OfType** method to return **OnlineCourses** related to the specified department.</span></span> <span data-ttu-id="e4ea4-157">3番目のクエリは、 **OnsiteCourses**を返します。</span><span class="sxs-lookup"><span data-stu-id="e4ea4-157">The third query returns **OnsiteCourses**.</span></span>

``` csharp
    using (var context = new SchoolEntities())
    {
        foreach (var department in context.Departments)
        {
            Console.WriteLine("The {0} department has the following courses:",
                               department.Name);

            Console.WriteLine("   All courses");
            foreach (var course in department.Courses )
            {
                Console.WriteLine("     {0}", course.Title);
            }

            foreach (var course in department.Courses.
                OfType<OnlineCourse>())
            {
                Console.WriteLine("   Online - {0}", course.Title);
            }

            foreach (var course in department.Courses.
                OfType<OnsiteCourse>())
            {
                Console.WriteLine("   Onsite - {0}", course.Title);
            }
        }
    }
```
