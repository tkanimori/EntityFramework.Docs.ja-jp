---
title: デザイナー TPT 継承 - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: efc78c31-b4ea-4ea3-a0cd-c69eb507020e
caps.latest.revision: 3
ms.openlocfilehash: c3ccb44f931b830a96a553d5af1e722a9ca4bbf0
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2018
ms.locfileid: "39122379"
---
# <a name="designer-tpt-inheritance"></a><span data-ttu-id="9228f-102">デザイナー TPT 継承</span><span class="sxs-lookup"><span data-stu-id="9228f-102">Designer TPT Inheritance</span></span>
<span data-ttu-id="9228f-103">このステップ バイ ステップ チュートリアルでは、Entity Framework デザイナー (EF Designer) を使用して、モデル内のテーブル、単位の種類 (TPT) 継承を実装する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="9228f-103">This step-by-step walkthrough shows how to implement table-per-type (TPT) inheritance in your model using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="9228f-104">Table-Per-Type 継承は、データベース内の別個のテーブルを使用して、非継承プロパティと継承階層のそれぞれの型のキー プロパティのデータを維持します。</span><span class="sxs-lookup"><span data-stu-id="9228f-104">Table-per-type inheritance uses a separate table in the database to maintain data for non-inherited properties and key properties for each type in the inheritance hierarchy.</span></span>

<span data-ttu-id="9228f-105">このチュートリアルでマップには、**コース**(基本データ型) **OnlineCourse** (コースから派生)、および**OnsiteCourse** (から派生**コース**)同じ名前を持つテーブルにエンティティ。</span><span class="sxs-lookup"><span data-stu-id="9228f-105">In this walkthrough we will map the **Course** (base type), **OnlineCourse** (derives from Course), and **OnsiteCourse** (derives from **Course**) entities to tables with the same names.</span></span> <span data-ttu-id="9228f-106">データベースからモデルを作成し、TPT 継承を実装するモデルを変更します。</span><span class="sxs-lookup"><span data-stu-id="9228f-106">We'll create a model from the database and then alter the model to implement the TPT inheritance.</span></span>

<span data-ttu-id="9228f-107">Model First でも起動し、モデルからデータベースを生成できます。</span><span class="sxs-lookup"><span data-stu-id="9228f-107">You can also start with the Model First and then generate the database from the model.</span></span> <span data-ttu-id="9228f-108">EF Designer では、既定で、TPT 戦略を使用して、モデルでは、何も継承が別個のテーブルにマップするためです。</span><span class="sxs-lookup"><span data-stu-id="9228f-108">The EF Designer uses the TPT strategy by default and so any inheritance in the model will be mapped to separate tables.</span></span>

## <a name="other-inheritance-options"></a><span data-ttu-id="9228f-109">その他の継承オプション</span><span class="sxs-lookup"><span data-stu-id="9228f-109">Other Inheritance Options</span></span>

<span data-ttu-id="9228f-110">Table-per-hierarchy (TPH) では、継承、継承階層内のエンティティ型のすべてのデータを維持するためにテーブルを使用する 1 つのデータベース内の別の型です。</span><span class="sxs-lookup"><span data-stu-id="9228f-110">Table-per-Hierarchy (TPH) is another type of inheritance in which one database table is used to maintain data for all of the entity types in an inheritance hierarchy.</span></span>  <span data-ttu-id="9228f-111">エンティティ デザイナーでの Table-per-hierarchy 継承をマップする方法については、次を参照してください。 [EF デザイナー TPH 継承](~/ef6/modeling/designer/inheritance/tph.md)します。</span><span class="sxs-lookup"><span data-stu-id="9228f-111">For information about how to map Table-per-Hierarchy inheritance with the Entity Designer, see [EF Designer TPH Inheritance](~/ef6/modeling/designer/inheritance/tph.md).</span></span> 

<span data-ttu-id="9228f-112">具象ごとのテーブル型 (TPC) 継承と混在の継承モデルは、Entity Framework ランタイムによってサポートされますが、EF Designer でサポートされていないに注意してください。</span><span class="sxs-lookup"><span data-stu-id="9228f-112">Note that, the Table-per-Concrete Type Inheritance (TPC) and mixed inheritance models are supported by the Entity Framework runtime but are not supported by the EF Designer.</span></span> <span data-ttu-id="9228f-113">2 つのオプションがある TPC または混在の継承を使用する場合は、: Code First を使用して、または EDMX ファイルを手動で編集します。</span><span class="sxs-lookup"><span data-stu-id="9228f-113">If you want to use TPC or mixed inheritance, you have two options: use Code First, or manually edit the EDMX file.</span></span> <span data-ttu-id="9228f-114">EDMX ファイルを使用する場合は、マッピングの詳細 ウィンドウは、「セーフ モード」に格納されます。 そして、デザイナーを使用して、マッピング変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="9228f-114">If you choose to work with the EDMX file, the Mapping Details Window will be put into “safe mode” and you will not be able to use the designer to change the mappings.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9228f-115">前提条件</span><span class="sxs-lookup"><span data-stu-id="9228f-115">Prerequisites</span></span>

<span data-ttu-id="9228f-116">このチュートリアルを完了するための要件を次に示します。</span><span class="sxs-lookup"><span data-stu-id="9228f-116">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="9228f-117">Visual Studio の最新バージョン。</span><span class="sxs-lookup"><span data-stu-id="9228f-117">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="9228f-118">[School サンプル データベース](~/ef6/resources/school-database.md)します。</span><span class="sxs-lookup"><span data-stu-id="9228f-118">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="9228f-119">プロジェクトを設定します。</span><span class="sxs-lookup"><span data-stu-id="9228f-119">Set up the Project</span></span>

-   <span data-ttu-id="9228f-120">Visual Studio 2012 を開きます。</span><span class="sxs-lookup"><span data-stu-id="9228f-120">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="9228f-121">選択**ファイル -&gt;新機能 -&gt;プロジェクト**</span><span class="sxs-lookup"><span data-stu-id="9228f-121">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="9228f-122">左側のウィンドウで次のようにクリックします。 **Visual C\#** を選び、**コンソール**テンプレート。</span><span class="sxs-lookup"><span data-stu-id="9228f-122">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="9228f-123">入力**TPTDBFirstSample**名として。</span><span class="sxs-lookup"><span data-stu-id="9228f-123">Enter **TPTDBFirstSample** as the name.</span></span>
-   <span data-ttu-id="9228f-124">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9228f-124">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="9228f-125">モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="9228f-125">Create a Model</span></span>

-   <span data-ttu-id="9228f-126">ソリューション エクスプ ローラーでプロジェクトを右クリックして**追加 -&gt;新しい項目の**します。</span><span class="sxs-lookup"><span data-stu-id="9228f-126">Right-click the project in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="9228f-127">選択**データ**選択し、左側のメニューから**ADO.NET Entity Data Model**テンプレート ペインでします。</span><span class="sxs-lookup"><span data-stu-id="9228f-127">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="9228f-128">入力**TPTModel.edmx**のファイル名、およびクリック**追加**します。</span><span class="sxs-lookup"><span data-stu-id="9228f-128">Enter **TPTModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="9228f-129">モデルのコンテンツの選択 ダイアログ ボックスを選択します \* \* \*、データベースから生成しをクリックして**次**します。</span><span class="sxs-lookup"><span data-stu-id="9228f-129">In the Choose Model Contents dialog box, select** Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="9228f-130">クリックして**新しい接続**します。</span><span class="sxs-lookup"><span data-stu-id="9228f-130">Click **New Connection**.</span></span>
    <span data-ttu-id="9228f-131">接続のプロパティ ダイアログ ボックスで、サーバー名を入力します (たとえば、 **(localdb)\\mssqllocaldb**) を選択します、認証方式として、型**学校**データベース名、し、。クリックして**OK**します。</span><span class="sxs-lookup"><span data-stu-id="9228f-131">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="9228f-132">データ接続の選択 ダイアログ ボックスは、データベース接続の設定で更新されます。</span><span class="sxs-lookup"><span data-stu-id="9228f-132">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="9228f-133">データベース オブジェクトの選択 ダイアログ ボックスの テーブル ノードを選択、**部門**、**コース、OnlineCourse、および OnsiteCourse**テーブル。</span><span class="sxs-lookup"><span data-stu-id="9228f-133">In the Choose Your Database Objects dialog box, under the Tables node, select the **Department**, **Course, OnlineCourse, and OnsiteCourse** tables.</span></span>
-   <span data-ttu-id="9228f-134">**[完了]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9228f-134">Click **Finish**.</span></span>

<span data-ttu-id="9228f-135">モデルを編集するため、デザイン サーフェイスを提供するエンティティ デザイナーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="9228f-135">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="9228f-136">[データベース オブジェクトの選択] ダイアログ ボックスで選択したすべてのオブジェクトは、モデルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="9228f-136">All the objects that you selected in the Choose Your Database Objects dialog box are added to the model.</span></span>

## <a name="implement-table-per-type-inheritance"></a><span data-ttu-id="9228f-137">テーブルの型ごとの継承を実装します。</span><span class="sxs-lookup"><span data-stu-id="9228f-137">Implement Table-per-Type Inheritance</span></span>

-   <span data-ttu-id="9228f-138">デザイン サーフェイスを右クリックし、 **OnlineCourse**エンティティの種類と選択**プロパティ**します。</span><span class="sxs-lookup"><span data-stu-id="9228f-138">On the design surface, right-click the **OnlineCourse** entity type and select **Properties**.</span></span>
-   <span data-ttu-id="9228f-139">**プロパティ**ウィンドウ、基本データ型プロパティを設定**コース**します。</span><span class="sxs-lookup"><span data-stu-id="9228f-139">In the **Properties** window, set the Base Type property to **Course**.</span></span>
-   <span data-ttu-id="9228f-140">右クリックし、 **OnsiteCourse**エンティティの種類と選択**プロパティ**します。</span><span class="sxs-lookup"><span data-stu-id="9228f-140">Right-click the **OnsiteCourse** entity type and select **Properties**.</span></span>
-   <span data-ttu-id="9228f-141">**プロパティ**ウィンドウ、基本データ型プロパティを設定**コース**します。</span><span class="sxs-lookup"><span data-stu-id="9228f-141">In the **Properties** window, set the Base Type property to **Course**.</span></span>
-   <span data-ttu-id="9228f-142">間のアソシエーション (線) を右クリックし、 **OnlineCourse**と**コース**エンティティ型。</span><span class="sxs-lookup"><span data-stu-id="9228f-142">Right-click the association (the line) between the **OnlineCourse** and **Course** entity types.</span></span>
    <span data-ttu-id="9228f-143">選択**モデルから削除**します。</span><span class="sxs-lookup"><span data-stu-id="9228f-143">Select **Delete from Model**.</span></span>
-   <span data-ttu-id="9228f-144">間の関連付けを右クリックし、 **OnsiteCourse**と**コース**エンティティ型。</span><span class="sxs-lookup"><span data-stu-id="9228f-144">Right-click the association between the **OnsiteCourse** and **Course** entity types.</span></span>
    <span data-ttu-id="9228f-145">選択**モデルから削除**します。</span><span class="sxs-lookup"><span data-stu-id="9228f-145">Select **Delete from Model**.</span></span>

<span data-ttu-id="9228f-146">私たちが削除されます、 **CourseID**プロパティから**OnlineCourse**と**OnsiteCourse**これらのクラスを継承するので、 **CourseID**から**コース**基本データ型。</span><span class="sxs-lookup"><span data-stu-id="9228f-146">We will now delete the **CourseID** property from **OnlineCourse** and **OnsiteCourse** because these classes inherit **CourseID** from the **Course** base type.</span></span>

-   <span data-ttu-id="9228f-147">右クリックし、 **CourseID**のプロパティ、 **OnlineCourse**エンティティ型、および選択**モデルから削除**します。</span><span class="sxs-lookup"><span data-stu-id="9228f-147">Right-click the **CourseID** property of the **OnlineCourse** entity type, and then select **Delete from Model**.</span></span>
-   <span data-ttu-id="9228f-148">右クリックし、 **CourseID**のプロパティ、 **OnsiteCourse**エンティティ型、および選択**モデルから削除**</span><span class="sxs-lookup"><span data-stu-id="9228f-148">Right-click the **CourseID** property of the **OnsiteCourse** entity type, and then select **Delete from Model**</span></span>
-   <span data-ttu-id="9228f-149">これで、Table-Per-Type 継承が実装されました。</span><span class="sxs-lookup"><span data-stu-id="9228f-149">Table-per-type inheritance is now implemented.</span></span>

![TPT](~/ef6/media/tpt.png)

## <a name="use-the-model"></a><span data-ttu-id="9228f-151">モデルを使用します。</span><span class="sxs-lookup"><span data-stu-id="9228f-151">Use the Model</span></span>

<span data-ttu-id="9228f-152">開く、 **Program.cs**ファイルの場所、 **Main**メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="9228f-152">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="9228f-153">次のコードを貼り付け、 **Main**関数。</span><span class="sxs-lookup"><span data-stu-id="9228f-153">Paste the following code into the **Main** function.</span></span> <span data-ttu-id="9228f-154">コードでは、次の 3 つのクエリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9228f-154">The code executes three queries.</span></span> <span data-ttu-id="9228f-155">最初のクエリを取り戻すすべて**コース**指定の部署に関連します。</span><span class="sxs-lookup"><span data-stu-id="9228f-155">The first query brings back all **Courses** related to the specified department.</span></span> <span data-ttu-id="9228f-156">2 番目のクエリを使用して、 **OfType**を返すメソッドを**OnlineCourses**指定の部署に関連します。</span><span class="sxs-lookup"><span data-stu-id="9228f-156">The second query uses the **OfType** method to return **OnlineCourses** related to the specified department.</span></span> <span data-ttu-id="9228f-157">3 番目のクエリが返す**OnsiteCourses**します。</span><span class="sxs-lookup"><span data-stu-id="9228f-157">The third query returns **OnsiteCourses**.</span></span>

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
