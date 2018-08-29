---
title: テーブル値関数 (Tvf) - EF6
author: divega
ms.date: 2016-10-23
ms.assetid: f019c97b-87b0-4e93-98f4-2c539f77b2dc
ms.openlocfilehash: f4b8c1bd442bac67a06584bd23c040381374f303
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993248"
---
# <a name="table-valued-functions-tvfs"></a><span data-ttu-id="8880d-102">テーブル値関数 (Tvf)</span><span class="sxs-lookup"><span data-stu-id="8880d-102">Table-Valued Functions (TVFs)</span></span>
> [!NOTE]
> <span data-ttu-id="8880d-103">**EF5 以降のみ**-機能、Api、Entity Framework 5 で導入されたなどのこのページで説明します。</span><span class="sxs-lookup"><span data-stu-id="8880d-103">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="8880d-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="8880d-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="8880d-105">ビデオ、およびステップ バイ ステップ チュートリアルでは、テーブル値関数 (Tvf、Entity Framework デザイナーを使用して) マップする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="8880d-105">The video and step-by-step walkthrough shows how to map table-valued functions (TVFs) using the Entity Framework Designer.</span></span> <span data-ttu-id="8880d-106">LINQ クエリから TVF を呼び出す方法も示します。</span><span class="sxs-lookup"><span data-stu-id="8880d-106">It also demonstrates how to call a TVF from a LINQ query.</span></span>

<span data-ttu-id="8880d-107">Tvf は現在、データベースの最初のワークフローでのみサポートされます。</span><span class="sxs-lookup"><span data-stu-id="8880d-107">TVFs are currently only supported in the Database First workflow.</span></span>

<span data-ttu-id="8880d-108">TVF のサポートは、Entity Framework バージョン 5 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="8880d-108">TVF support was introduced in Entity Framework version 5.</span></span> <span data-ttu-id="8880d-109">テーブル値関数、列挙型、および .NET Framework 4.5 をターゲットする必要がありますの空間型などの新機能を使用するように注意してください。</span><span class="sxs-lookup"><span data-stu-id="8880d-109">Note that to use the new features like table-valued functions, enums, and spatial types you must target .NET Framework 4.5.</span></span> <span data-ttu-id="8880d-110">Visual Studio 2012 では、既定では .NET 4.5 を対象とします。</span><span class="sxs-lookup"><span data-stu-id="8880d-110">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="8880d-111">Tvf が 1 つの主な違いを使用したストアド プロシージャとよく似ています。 TVF の結果はコンポーザブルです。</span><span class="sxs-lookup"><span data-stu-id="8880d-111">TVFs are very similar to stored procedures with one key difference: the result of a TVF is composable.</span></span> <span data-ttu-id="8880d-112">つまり、TVF の結果は、ストアド プロシージャの結果ができないときに、LINQ クエリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="8880d-112">That means the results from a TVF can be used in a LINQ query while the results of a stored procedure cannot.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="8880d-113">ビデオを見る</span><span class="sxs-lookup"><span data-stu-id="8880d-113">Watch the video</span></span>

<span data-ttu-id="8880d-114">**によって提示される**: Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="8880d-114">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="8880d-115">[WMV](http://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.wmv) | [MP4](http://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-mp4video-tvf.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.zip)</span><span class="sxs-lookup"><span data-stu-id="8880d-115">[WMV](http://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.wmv) | [MP4](http://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-mp4video-tvf.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="8880d-116">前提条件</span><span class="sxs-lookup"><span data-stu-id="8880d-116">Pre-Requisites</span></span>

<span data-ttu-id="8880d-117">このチュートリアルを完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8880d-117">To complete this walkthrough, you need to:</span></span>

- <span data-ttu-id="8880d-118">インストール、 [School データベース](~/ef6/resources/school-database.md)します。</span><span class="sxs-lookup"><span data-stu-id="8880d-118">Install the [School database](~/ef6/resources/school-database.md).</span></span>

- <span data-ttu-id="8880d-119">Visual Studio の最新バージョンします。</span><span class="sxs-lookup"><span data-stu-id="8880d-119">Have a recent version of Visual Studio</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="8880d-120">プロジェクトを設定します。</span><span class="sxs-lookup"><span data-stu-id="8880d-120">Set up the Project</span></span>

1.  <span data-ttu-id="8880d-121">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="8880d-121">Open Visual Studio</span></span>
2.  <span data-ttu-id="8880d-122">**ファイル**メニューで、**新規**、 をクリックし、**プロジェクト**</span><span class="sxs-lookup"><span data-stu-id="8880d-122">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="8880d-123">左側のウィンドウで次のようにクリックします**Visual C\#** を選び、**コンソール**テンプレート。</span><span class="sxs-lookup"><span data-stu-id="8880d-123">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="8880d-124">入力**TVF**をクリックして、プロジェクトの名前として**OK**</span><span class="sxs-lookup"><span data-stu-id="8880d-124">Enter **TVF** as the name of the project and click **OK**</span></span>

## <a name="add-a-tvf-to-the-database"></a><span data-ttu-id="8880d-125">TVF をデータベースに追加します。</span><span class="sxs-lookup"><span data-stu-id="8880d-125">Add a TVF to the Database</span></span>

-   <span data-ttu-id="8880d-126">選択**ビュー -&gt; SQL Server オブジェクト エクスプ ローラー**</span><span class="sxs-lookup"><span data-stu-id="8880d-126">Select **View -&gt; SQL Server Object Explorer**</span></span>
-   <span data-ttu-id="8880d-127">LocalDB はサーバーの一覧にない場合: を右クリックし**SQL Server**選択**SQL Server の追加**既定値を使用して、 **Windows 認証**LocalDB サーバーに接続するには</span><span class="sxs-lookup"><span data-stu-id="8880d-127">If LocalDB is not in the list of servers: Right-click on **SQL Server** and select **Add SQL Server** Use the default **Windows Authentication** to connect to the LocalDB server</span></span>
-   <span data-ttu-id="8880d-128">LocalDB のノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="8880d-128">Expand the LocalDB node</span></span>
-   <span data-ttu-id="8880d-129">[データベース] ノードでは、School データベース ノードを右クリックして**新しいクエリ.**</span><span class="sxs-lookup"><span data-stu-id="8880d-129">Under the Databases node, right-click the School database node and select **New Query…**</span></span>
-   <span data-ttu-id="8880d-130">T-SQL エディターでは、次の TVF 定義を貼り付け</span><span class="sxs-lookup"><span data-stu-id="8880d-130">In T-SQL Editor, paste the following TVF definition</span></span>

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

-   <span data-ttu-id="8880d-131">T-SQL エディターでマウスの右ボタンをクリックし、選択**Execute**</span><span class="sxs-lookup"><span data-stu-id="8880d-131">Click the right mouse button on the T-SQL editor and select **Execute**</span></span>
-   <span data-ttu-id="8880d-132">GetStudentGradesForCourse 関数は、School データベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="8880d-132">The GetStudentGradesForCourse function is added to the School database</span></span>

 

## <a name="create-a-model"></a><span data-ttu-id="8880d-133">モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="8880d-133">Create a Model</span></span>

1.  <span data-ttu-id="8880d-134">ソリューション エクスプ ローラーでプロジェクト名を右クリックし、[**追加**、] をクリックし、**新しい項目**</span><span class="sxs-lookup"><span data-stu-id="8880d-134">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="8880d-135">選択**データ**選択し、左側のメニューから**ADO.NET Entity Data Model**で、**テンプレート**ウィンドウ</span><span class="sxs-lookup"><span data-stu-id="8880d-135">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the **Templates** pane</span></span>
3.  <span data-ttu-id="8880d-136">入力**TVFModel.edmx**のファイル名、およびクリック**追加**</span><span class="sxs-lookup"><span data-stu-id="8880d-136">Enter **TVFModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="8880d-137">モデルのコンテンツの選択 ダイアログ ボックスで、次のように選択します**データベースから生成**、 をクリックし、 **次へ。**</span><span class="sxs-lookup"><span data-stu-id="8880d-137">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**</span></span>
5.  <span data-ttu-id="8880d-138">クリックして**新しい接続**」と入力 **(localdb)\\mssqllocaldb**サーバー名テキスト ボックスに、Enter**学校**データベースの名前をクリックします **[ok]**</span><span class="sxs-lookup"><span data-stu-id="8880d-138">Click **New Connection** Enter **(localdb)\\mssqllocaldb** in the Server name text box Enter **School** for the database name Click **OK**</span></span>
6.  <span data-ttu-id="8880d-139">選択 で、データベース オブジェクト ダイアログ ボックスで、**テーブル**ノードを選択、 **Person**、 **StudentGrade**、および**コース**テーブル</span><span class="sxs-lookup"><span data-stu-id="8880d-139">In the Choose Your Database Objects dialog box, under the **Tables** node, select the **Person**, **StudentGrade**, and **Course** tables</span></span>
7.  <span data-ttu-id="8880d-140">選択、 **GetStudentGradesForCourse**関数の下にある、**ストアド プロシージャおよび関数**注、Visual Studio 2012 では、その開始ノード、エンティティ デザイナーを使用するバッチのインポート をストアド プロシージャおよび関数</span><span class="sxs-lookup"><span data-stu-id="8880d-140">Select the **GetStudentGradesForCourse** function located under the **Stored Procedures and Functions** node Note, that starting with Visual Studio 2012, the Entity Designer allows you to batch import your Stored Procedures and Functions</span></span>
8.  <span data-ttu-id="8880d-141">クリックして**完了**</span><span class="sxs-lookup"><span data-stu-id="8880d-141">Click **Finish**</span></span>
9.  <span data-ttu-id="8880d-142">モデルを編集するため、デザイン サーフェイスを提供するエンティティ デザイナーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="8880d-142">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="8880d-143">選択したすべてのオブジェクト、**データベース オブジェクトの選択** ダイアログ ボックスは、モデルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="8880d-143">All the objects that you selected in the **Choose Your Database Objects** dialog box are added to the model.</span></span>
10. <span data-ttu-id="8880d-144">既定では、各インポートされたストアド プロシージャまたは関数の結果の形式は、エンティティ モデル内の新しい複合型に自動的になります。</span><span class="sxs-lookup"><span data-stu-id="8880d-144">By default, the result shape of each imported stored procedure or function will automatically become a new complex type in your entity model.</span></span> <span data-ttu-id="8880d-145">GetStudentGradesForCourse 関数の結果を StudentGrade エンティティにマップします選択し、画面のデザインを右クリック**モデル ブラウザー**モデル ブラウザーで、選択**関数インポート**。、し、ダブルクリック、 **GetStudentGradesForCourse**関数で、関数インポートの編集] ダイアログ ボックス、[**エンティティ**選択**StudentGrade**</span><span class="sxs-lookup"><span data-stu-id="8880d-145">But we want to map the results of the GetStudentGradesForCourse function to the StudentGrade entity: Right-click the design surface and select **Model Browser** In Model Browser, select **Function Imports**, and then double-click the **GetStudentGradesForCourse** function In the Edit Function Import dialog box, select **Entities** and choose **StudentGrade**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="8880d-146">永続化およびデータの取得</span><span class="sxs-lookup"><span data-stu-id="8880d-146">Persist and Retrieve Data</span></span>

<span data-ttu-id="8880d-147">Main メソッドが定義されているファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="8880d-147">Open the file where the Main method is defined.</span></span> <span data-ttu-id="8880d-148">Main 関数には、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="8880d-148">Add the following code into the Main function.</span></span>

<span data-ttu-id="8880d-149">次のコードでは、テーブル値関数を使用するクエリを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="8880d-149">The following code demonstrates how to build a query that uses a Table-valued Function.</span></span> <span data-ttu-id="8880d-150">クエリでは、関連のコースのタイトルと 3.5 以上のレベルが関連の受講者を含む匿名型に結果を射影します。</span><span class="sxs-lookup"><span data-stu-id="8880d-150">The query projects the results into an anonymous type that contains the related Course title and related students with a grade greater or equal to 3.5.</span></span>

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

<span data-ttu-id="8880d-151">アプリケーションをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="8880d-151">Compile and run the application.</span></span> <span data-ttu-id="8880d-152">このプログラムの出力は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8880d-152">The program produces the following output:</span></span>

```
Couse: Microeconomics, Student: Arturo Anand
Couse: Microeconomics, Student: Carson Bryant
```

## <a name="summary"></a><span data-ttu-id="8880d-153">まとめ</span><span class="sxs-lookup"><span data-stu-id="8880d-153">Summary</span></span>

<span data-ttu-id="8880d-154">このチュートリアルでは、テーブル値関数 (Tvf、Entity Framework デザイナーを使用して) マップする方法を説明しました。</span><span class="sxs-lookup"><span data-stu-id="8880d-154">In this walkthrough we looked at how to map Table-valued Functions (TVFs) using the Entity Framework Designer.</span></span> <span data-ttu-id="8880d-155">LINQ クエリから TVF を呼び出す方法も示されています。</span><span class="sxs-lookup"><span data-stu-id="8880d-155">It also demonstrated how to call a TVF from a LINQ query.</span></span>
