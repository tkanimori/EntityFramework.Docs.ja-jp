---
title: テーブル値関数 (Tvf)-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: f019c97b-87b0-4e93-98f4-2c539f77b2dc
ms.openlocfilehash: 35684196dcd7b708a8feeb1eca3096e8d4e555ec
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182533"
---
# <a name="table-valued-functions-tvfs"></a><span data-ttu-id="bcdc2-102">テーブル値関数 (Tvf)</span><span class="sxs-lookup"><span data-stu-id="bcdc2-102">Table-Valued Functions (TVFs)</span></span>
> [!NOTE]
> <span data-ttu-id="bcdc2-103">**EF5**以降: このページで説明した機能、api などは、Entity Framework 5 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-103">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="bcdc2-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="bcdc2-105">ビデオとステップバイステップのチュートリアルでは、Entity Framework Designer を使用してテーブル値関数 (Tvf) をマップする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-105">The video and step-by-step walkthrough shows how to map table-valued functions (TVFs) using the Entity Framework Designer.</span></span> <span data-ttu-id="bcdc2-106">また、LINQ クエリから TVF を呼び出す方法についても説明します。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-106">It also demonstrates how to call a TVF from a LINQ query.</span></span>

<span data-ttu-id="bcdc2-107">Tvf は現在、Database First ワークフローでのみサポートされています。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-107">TVFs are currently only supported in the Database First workflow.</span></span>

<span data-ttu-id="bcdc2-108">TVF サポートは Entity Framework version 5 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-108">TVF support was introduced in Entity Framework version 5.</span></span> <span data-ttu-id="bcdc2-109">テーブル値関数、列挙型、空間型などの新機能を使用するには、.NET Framework 4.5 をターゲットにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-109">Note that to use the new features like table-valued functions, enums, and spatial types you must target .NET Framework 4.5.</span></span> <span data-ttu-id="bcdc2-110">既定では、Visual Studio 2012 は .NET 4.5 を対象としています。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-110">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="bcdc2-111">Tvf はストアドプロシージャとよく似ていますが、1つの重要な違いがあります。 TVF の結果は、コンポーザブルです。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-111">TVFs are very similar to stored procedures with one key difference: the result of a TVF is composable.</span></span> <span data-ttu-id="bcdc2-112">つまり、TVF の結果は LINQ クエリで使用できますが、ストアドプロシージャの結果は使用できません。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-112">That means the results from a TVF can be used in a LINQ query while the results of a stored procedure cannot.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="bcdc2-113">ビデオを見る</span><span class="sxs-lookup"><span data-stu-id="bcdc2-113">Watch the video</span></span>

<span data-ttu-id="bcdc2-114">**表示者**:ジュリアつい</span><span class="sxs-lookup"><span data-stu-id="bcdc2-114">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="bcdc2-115">[WMV](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.wmv) | [MP4](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-mp4video-tvf.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.zip)</span><span class="sxs-lookup"><span data-stu-id="bcdc2-115">[WMV](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.wmv) | [MP4](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-mp4video-tvf.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="bcdc2-116">前提条件</span><span class="sxs-lookup"><span data-stu-id="bcdc2-116">Pre-Requisites</span></span>

<span data-ttu-id="bcdc2-117">このチュートリアルを完了するには、次の手順を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-117">To complete this walkthrough, you need to:</span></span>

- <span data-ttu-id="bcdc2-118">[School データベース](~/ef6/resources/school-database.md)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-118">Install the [School database](~/ef6/resources/school-database.md).</span></span>

- <span data-ttu-id="bcdc2-119">Visual Studio の最新バージョンがあること</span><span class="sxs-lookup"><span data-stu-id="bcdc2-119">Have a recent version of Visual Studio</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="bcdc2-120">プロジェクトを設定する</span><span class="sxs-lookup"><span data-stu-id="bcdc2-120">Set up the Project</span></span>

1.  <span data-ttu-id="bcdc2-121">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="bcdc2-121">Open Visual Studio</span></span>
2.  <span data-ttu-id="bcdc2-122">**[ファイル]** メニューの **[新規作成]** をポイントし、 **[プロジェクト]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-122">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="bcdc2-123">左側のウィンドウで、 **[Visual C @ no__t-1]** をクリックし、**コンソール**テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-123">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="bcdc2-124">プロジェクトの名前として「 **TVF** 」と入力し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-124">Enter **TVF** as the name of the project and click **OK**</span></span>

## <a name="add-a-tvf-to-the-database"></a><span data-ttu-id="bcdc2-125">TVF をデータベースに追加する</span><span class="sxs-lookup"><span data-stu-id="bcdc2-125">Add a TVF to the Database</span></span>

-   <span data-ttu-id="bcdc2-126">[**表示-&gt;** ] を選択し SQL Server オブジェクトエクスプローラー</span><span class="sxs-lookup"><span data-stu-id="bcdc2-126">Select **View -&gt; SQL Server Object Explorer**</span></span>
-   <span data-ttu-id="bcdc2-127">LocalDB がサーバーの一覧にない場合は、次のようになります。**SQL Server**を右クリックして **[追加]** を選択 SQL Server、既定の**Windows 認証**を使用して LocalDB サーバーに接続します。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-127">If LocalDB is not in the list of servers: Right-click on **SQL Server** and select **Add SQL Server** Use the default **Windows Authentication** to connect to the LocalDB server</span></span>
-   <span data-ttu-id="bcdc2-128">LocalDB ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-128">Expand the LocalDB node</span></span>
-   <span data-ttu-id="bcdc2-129">[データベース] ノードで、School データベースノードを右クリックし、[ **新しいクエリ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-129">Under the Databases node, right-click the School database node and select **New Query…**</span></span>
-   <span data-ttu-id="bcdc2-130">T-sql エディターで、次の TVF 定義を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-130">In T-SQL Editor, paste the following TVF definition</span></span>

``` SQL
CREATE FUNCTION [dbo].[GetStudentGradesForCourse]

(@CourseID INT)

RETURNS TABLE

RETURN
    SELECT [EnrollmentID],
           [CourseID],
           [StudentID],
           [Grade]
    FROM   [dbo].[StudentGrade]
    WHERE  CourseID = @CourseID
```

-   <span data-ttu-id="bcdc2-131">T-sql エディターでマウスの右ボタンをクリックし、[ **実行**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-131">Click the right mouse button on the T-SQL editor and select **Execute**</span></span>
-   <span data-ttu-id="bcdc2-132">GetStudentGradesForCourse 関数が School データベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-132">The GetStudentGradesForCourse function is added to the School database</span></span>

 

## <a name="create-a-model"></a><span data-ttu-id="bcdc2-133">モデルの作成</span><span class="sxs-lookup"><span data-stu-id="bcdc2-133">Create a Model</span></span>

1.  <span data-ttu-id="bcdc2-134">ソリューションエクスプローラーでプロジェクト名を右クリックし、 **[追加]** をポイントして、 **[新しい項目]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-134">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="bcdc2-135">左側のメニューから **[データ]** を選択し、 **[テンプレート]** ペインで **[ADO.NET Entity Data Model]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-135">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the **Templates** pane</span></span>
3.  <span data-ttu-id="bcdc2-136">ファイル名として「TVFModel」と入力し、 **[追加]** をクリックし**ます。**</span><span class="sxs-lookup"><span data-stu-id="bcdc2-136">Enter **TVFModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="bcdc2-137">[モデルのコンテンツの選択] ダイアログボックスで、[ **データベースから生成**] を選択し、[ **次へ**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-137">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**</span></span>
5.  <span data-ttu-id="bcdc2-138">[ **新しい接続**] をクリックし @no__t て、[サーバー名] テキストボックスに「 **School** for と入力し、データベース名として「 **2mssqllocaldb** 」と入力し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-138">Click **New Connection** Enter **(localdb)\\mssqllocaldb** in the Server name text box Enter **School** for the database name Click **OK**</span></span>
6.  <span data-ttu-id="bcdc2-139">[データベースオブジェクトの選択] ダイアログボックスの [ **テーブル** ] ノードで、 **Person**、 **StudentGrade**、および **Course** tables を選択します。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-139">In the Choose Your Database Objects dialog box, under the **Tables** node, select the **Person**, **StudentGrade**, and **Course** tables</span></span>
7.  <span data-ttu-id="bcdc2-140"> **ストアドプロシージャと関数** Node メモの下にある **GetStudentGradesForCourse**関数を選択します。 Visual Studio 2012 以降では、Entity Designer を使用して、ストアドプロシージャと関数を一括インポートできます。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-140">Select the **GetStudentGradesForCourse** function located under the **Stored Procedures and Functions** node Note, that starting with Visual Studio 2012, the Entity Designer allows you to batch import your Stored Procedures and Functions</span></span>
8.  <span data-ttu-id="bcdc2-141">[ **完了**] をクリック</span><span class="sxs-lookup"><span data-stu-id="bcdc2-141">Click **Finish**</span></span>
9.  <span data-ttu-id="bcdc2-142">モデルを編集するためのデザイン画面を提供する Entity Designer が表示されます。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-142">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="bcdc2-143">[ **データベースオブジェクトの選択** ] ダイアログボックスで選択したすべてのオブジェクトがモデルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-143">All the objects that you selected in the **Choose Your Database Objects** dialog box are added to the model.</span></span>
10. <span data-ttu-id="bcdc2-144">既定では、インポートされた各ストアドプロシージャまたは関数の結果構造は、自動的にエンティティモデルの新しい複合型になります。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-144">By default, the result shape of each imported stored procedure or function will automatically become a new complex type in your entity model.</span></span> <span data-ttu-id="bcdc2-145">しかし、GetStudentGradesForCourse 関数の結果を StudentGrade エンティティにマップする必要があります。デザインサーフェイスを右クリックし、モデルブラウザーで [ **モデルブラウザー** ] を選択します。次に、[ **関数**インポート] を選択し、[関数インポートの編集] ダイアログボックスで **GetStudentGradesForCourse**関数をダブルクリックして、[ **エンティティ**] を選択します。 and **StudentGrade**を選択します</span><span class="sxs-lookup"><span data-stu-id="bcdc2-145">But we want to map the results of the GetStudentGradesForCourse function to the StudentGrade entity: Right-click the design surface and select **Model Browser** In Model Browser, select **Function Imports**, and then double-click the **GetStudentGradesForCourse** function In the Edit Function Import dialog box, select **Entities** and choose **StudentGrade**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="bcdc2-146">データの永続化と取得</span><span class="sxs-lookup"><span data-stu-id="bcdc2-146">Persist and Retrieve Data</span></span>

<span data-ttu-id="bcdc2-147">Main メソッドが定義されているファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-147">Open the file where the Main method is defined.</span></span> <span data-ttu-id="bcdc2-148">Main 関数に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-148">Add the following code into the Main function.</span></span>

<span data-ttu-id="bcdc2-149">次のコードは、テーブル値関数を使用するクエリを作成する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-149">The following code demonstrates how to build a query that uses a Table-valued Function.</span></span> <span data-ttu-id="bcdc2-150">このクエリでは、関連するコースのタイトルと、3.5 以上のレベルを持つ関連する学生を含む匿名型に結果を射影します。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-150">The query projects the results into an anonymous type that contains the related Course title and related students with a grade greater or equal to 3.5.</span></span>

``` csharp
using (var context = new SchoolEntities())
{
    var CourseID = 4022;
    var Grade = 3.5M;

    // Return all the best students in the Microeconomics class.
    var students = from s in context.GetStudentGradesForCourse(CourseID)
                            where s.Grade >= Grade
                            select new
                            {
                                s.Person,
                                s.Course.Title
                            };

    foreach (var result in students)
    {
        Console.WriteLine(
            "Couse: {0}, Student: {1} {2}",
            result.Title,  
            result.Person.FirstName,  
            result.Person.LastName);
    }
}
```

<span data-ttu-id="bcdc2-151">アプリケーションをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-151">Compile and run the application.</span></span> <span data-ttu-id="bcdc2-152">このプログラムの出力は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-152">The program produces the following output:</span></span>

```console
Couse: Microeconomics, Student: Arturo Anand
Couse: Microeconomics, Student: Carson Bryant
```

## <a name="summary"></a><span data-ttu-id="bcdc2-153">まとめ</span><span class="sxs-lookup"><span data-stu-id="bcdc2-153">Summary</span></span>

<span data-ttu-id="bcdc2-154">このチュートリアルでは、Entity Framework Designer を使用してテーブル値関数 (Tvf) をマップする方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-154">In this walkthrough we looked at how to map Table-valued Functions (TVFs) using the Entity Framework Designer.</span></span> <span data-ttu-id="bcdc2-155">また、LINQ クエリから TVF を呼び出す方法についても説明します。</span><span class="sxs-lookup"><span data-stu-id="bcdc2-155">It also demonstrated how to call a TVF from a LINQ query.</span></span>
