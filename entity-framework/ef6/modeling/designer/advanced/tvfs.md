---
title: Table-Valued 関数 (Tvf)-EF6
description: Entity Framework 6 の Table-Valued 関数 (Tvf)
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/tvfs
ms.openlocfilehash: 2c699cad3ace253842d17aca4eca4df2318c4795
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066201"
---
# <a name="table-valued-functions-tvfs"></a><span data-ttu-id="aedcc-103">Table-Valued 関数 (Tvf)</span><span class="sxs-lookup"><span data-stu-id="aedcc-103">Table-Valued Functions (TVFs)</span></span>
> [!NOTE]
> <span data-ttu-id="aedcc-104">**EF5** 以降: このページで説明した機能、api などは、Entity Framework 5 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="aedcc-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="aedcc-105">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="aedcc-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="aedcc-106">ビデオとステップバイステップのチュートリアルでは、Entity Framework Designer を使用してテーブル値関数 (Tvf) をマップする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="aedcc-106">The video and step-by-step walkthrough shows how to map table-valued functions (TVFs) using the Entity Framework Designer.</span></span> <span data-ttu-id="aedcc-107">また、LINQ クエリから TVF を呼び出す方法についても説明します。</span><span class="sxs-lookup"><span data-stu-id="aedcc-107">It also demonstrates how to call a TVF from a LINQ query.</span></span>

<span data-ttu-id="aedcc-108">Tvf は現在、Database First ワークフローでのみサポートされています。</span><span class="sxs-lookup"><span data-stu-id="aedcc-108">TVFs are currently only supported in the Database First workflow.</span></span>

<span data-ttu-id="aedcc-109">TVF サポートは Entity Framework version 5 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="aedcc-109">TVF support was introduced in Entity Framework version 5.</span></span> <span data-ttu-id="aedcc-110">テーブル値関数、列挙型、空間型などの新機能を使用するには、.NET Framework 4.5 をターゲットにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="aedcc-110">Note that to use the new features like table-valued functions, enums, and spatial types you must target .NET Framework 4.5.</span></span> <span data-ttu-id="aedcc-111">既定では、Visual Studio 2012 は .NET 4.5 を対象としています。</span><span class="sxs-lookup"><span data-stu-id="aedcc-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="aedcc-112">Tvf はストアドプロシージャとよく似ていますが、1つの重要な違いがあります。 TVF の結果は、コンポーザブルです。</span><span class="sxs-lookup"><span data-stu-id="aedcc-112">TVFs are very similar to stored procedures with one key difference: the result of a TVF is composable.</span></span> <span data-ttu-id="aedcc-113">つまり、TVF の結果は LINQ クエリで使用できますが、ストアドプロシージャの結果は使用できません。</span><span class="sxs-lookup"><span data-stu-id="aedcc-113">That means the results from a TVF can be used in a LINQ query while the results of a stored procedure cannot.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="aedcc-114">ビデオを見る</span><span class="sxs-lookup"><span data-stu-id="aedcc-114">Watch the video</span></span>

<span data-ttu-id="aedcc-115">**表示者**: ジュリアつい</span><span class="sxs-lookup"><span data-stu-id="aedcc-115">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="aedcc-116">[WMV](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.wmv)  | [MP4](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-mp4video-tvf.m4v)  | [WMV (ZIP)](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.zip)</span><span class="sxs-lookup"><span data-stu-id="aedcc-116">[WMV](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.wmv) | [MP4](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-mp4video-tvf.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="aedcc-117">前提条件</span><span class="sxs-lookup"><span data-stu-id="aedcc-117">Pre-Requisites</span></span>

<span data-ttu-id="aedcc-118">このチュートリアルを完了するには、次の手順を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="aedcc-118">To complete this walkthrough, you need to:</span></span>

- <span data-ttu-id="aedcc-119">[School データベース](xref:ef6/resources/school-database)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="aedcc-119">Install the [School database](xref:ef6/resources/school-database).</span></span>

- <span data-ttu-id="aedcc-120">Visual Studio の最新バージョンがあること</span><span class="sxs-lookup"><span data-stu-id="aedcc-120">Have a recent version of Visual Studio</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="aedcc-121">プロジェクトを設定する</span><span class="sxs-lookup"><span data-stu-id="aedcc-121">Set up the Project</span></span>

1.  <span data-ttu-id="aedcc-122">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="aedcc-122">Open Visual Studio</span></span>
2.  <span data-ttu-id="aedcc-123">[**ファイル**] メニューの [**新規作成**] をポイントし、[**プロジェクト**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="aedcc-123">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="aedcc-124">左側のウィンドウで、[ **Visual C \# **] をクリックし、**コンソール**テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="aedcc-124">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="aedcc-125">プロジェクトの名前として「 **TVF** 」と入力し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="aedcc-125">Enter **TVF** as the name of the project and click **OK**</span></span>

## <a name="add-a-tvf-to-the-database"></a><span data-ttu-id="aedcc-126">TVF をデータベースに追加する</span><span class="sxs-lookup"><span data-stu-id="aedcc-126">Add a TVF to the Database</span></span>

-   <span data-ttu-id="aedcc-127">**ビュー &gt; SQL Server オブジェクトエクスプローラーの**選択</span><span class="sxs-lookup"><span data-stu-id="aedcc-127">Select **View -&gt; SQL Server Object Explorer**</span></span>
-   <span data-ttu-id="aedcc-128">LocalDB がサーバーの一覧にない場合: **SQL Server** を右クリックし、[追加] を選択 **SQL Server** 既定の **Windows 認証** を使用して localdb サーバーに接続する</span><span class="sxs-lookup"><span data-stu-id="aedcc-128">If LocalDB is not in the list of servers: Right-click on **SQL Server** and select **Add SQL Server** Use the default **Windows Authentication** to connect to the LocalDB server</span></span>
-   <span data-ttu-id="aedcc-129">LocalDB ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="aedcc-129">Expand the LocalDB node</span></span>
-   <span data-ttu-id="aedcc-130">[データベース] ノードで、School データベースノードを右クリックし、[ **新しいクエリ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="aedcc-130">Under the Databases node, right-click the School database node and select **New Query…**</span></span>
-   <span data-ttu-id="aedcc-131">T-sql エディターで、次の TVF 定義を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="aedcc-131">In T-SQL Editor, paste the following TVF definition</span></span>

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

-   <span data-ttu-id="aedcc-132">T-sql エディターでマウスの右ボタンをクリックし、[ **実行**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="aedcc-132">Click the right mouse button on the T-SQL editor and select **Execute**</span></span>
-   <span data-ttu-id="aedcc-133">GetStudentGradesForCourse 関数が School データベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="aedcc-133">The GetStudentGradesForCourse function is added to the School database</span></span>

 

## <a name="create-a-model"></a><span data-ttu-id="aedcc-134">モデルの作成</span><span class="sxs-lookup"><span data-stu-id="aedcc-134">Create a Model</span></span>

1.  <span data-ttu-id="aedcc-135">ソリューションエクスプローラーでプロジェクト名を右クリックし、[**追加**] をポイントして、[**新しい項目**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="aedcc-135">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="aedcc-136">左側のメニューから [**データ**] を選択し、[**テンプレート**] ペインで [ **ADO.NET Entity Data Model** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="aedcc-136">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the **Templates** pane</span></span>
3.  <span data-ttu-id="aedcc-137">ファイル名として「TVFModel」と入力し、[**追加**] をクリックし**ます。**</span><span class="sxs-lookup"><span data-stu-id="aedcc-137">Enter **TVFModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="aedcc-138">[モデルのコンテンツの選択] ダイアログボックスで、[ **データベースから生成**] を選択し、[ **次へ**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="aedcc-138">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**</span></span>
5.  <span data-ttu-id="aedcc-139">[ **新しい接続**] をクリックします。 [サーバー名] ボックスに**School**「 \*\* \\ mssqllocaldb\*\* 」と入力し、   データベース名として「School」と入力します。 [ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="aedcc-139">Click **New Connection** Enter **(localdb)\\mssqllocaldb** in the Server name text box Enter **School** for the database name Click **OK**</span></span>
6.  <span data-ttu-id="aedcc-140">[データベースオブジェクトの選択] ダイアログボックスの [ **テーブル**] ノードで、    **Person**、 **StudentGrade**、および **Course**の各テーブルを選択します。  </span><span class="sxs-lookup"><span data-stu-id="aedcc-140">In the Choose Your Database Objects dialog box, under the **Tables** node, select the **Person**, **StudentGrade**, and **Course** tables</span></span>
7.  <span data-ttu-id="aedcc-141">[ **ストアドプロシージャと関数**] ノードの下にある **GetStudentGradesForCourse**関数を選択します。これは、   Visual Studio Entity Designer 2012 以降では、ストアドプロシージャと関数をバッチインポートすることができます。</span><span class="sxs-lookup"><span data-stu-id="aedcc-141">Select the **GetStudentGradesForCourse** function located under the **Stored Procedures and Functions** node Note, that starting with Visual Studio 2012, the Entity Designer allows you to batch import your Stored Procedures and Functions</span></span>
8.  <span data-ttu-id="aedcc-142">[ **完了**] をクリック</span><span class="sxs-lookup"><span data-stu-id="aedcc-142">Click **Finish**</span></span>
9.  <span data-ttu-id="aedcc-143">モデルを編集するためのデザイン画面を提供する Entity Designer が表示されます。</span><span class="sxs-lookup"><span data-stu-id="aedcc-143">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="aedcc-144">[ **データベースオブジェクトの選択**] ダイアログボックスで選択したすべてのオブジェクト   がモデルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="aedcc-144">All the objects that you selected in the **Choose Your Database Objects** dialog box are added to the model.</span></span>
10. <span data-ttu-id="aedcc-145">既定では、インポートされた各ストアドプロシージャまたは関数の結果構造は、自動的にエンティティモデルの新しい複合型になります。</span><span class="sxs-lookup"><span data-stu-id="aedcc-145">By default, the result shape of each imported stored procedure or function will automatically become a new complex type in your entity model.</span></span> <span data-ttu-id="aedcc-146">ただし、GetStudentGradesForCourse 関数の結果を StudentGrade エンティティにマップする必要があります。デザイン画面を右クリックし、モデルブラウザーで [ **モデルブラウザー** ] を選択し、[ **関数**インポート] を選択します。次に、[関数インポートの編集] ダイアログボックスで **GetStudentGradesForCourse**関数をダブルクリックし、[ **エンティティ**] を選択し   て [ **StudentGrade** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="aedcc-146">But we want to map the results of the GetStudentGradesForCourse function to the StudentGrade entity: Right-click the design surface and select **Model Browser** In Model Browser, select **Function Imports**, and then double-click the **GetStudentGradesForCourse** function In the Edit Function Import dialog box, select **Entities** and choose **StudentGrade**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="aedcc-147">データの永続化と取得</span><span class="sxs-lookup"><span data-stu-id="aedcc-147">Persist and Retrieve Data</span></span>

<span data-ttu-id="aedcc-148">Main メソッドが定義されているファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="aedcc-148">Open the file where the Main method is defined.</span></span> <span data-ttu-id="aedcc-149">Main 関数に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="aedcc-149">Add the following code into the Main function.</span></span>

<span data-ttu-id="aedcc-150">次のコードは、テーブル値関数を使用するクエリを作成する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="aedcc-150">The following code demonstrates how to build a query that uses a Table-valued Function.</span></span> <span data-ttu-id="aedcc-151">このクエリでは、関連するコースのタイトルと、3.5 以上のレベルを持つ関連する学生を含む匿名型に結果を射影します。</span><span class="sxs-lookup"><span data-stu-id="aedcc-151">The query projects the results into an anonymous type that contains the related Course title and related students with a grade greater or equal to 3.5.</span></span>

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

<span data-ttu-id="aedcc-152">アプリケーションをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="aedcc-152">Compile and run the application.</span></span> <span data-ttu-id="aedcc-153">このプログラムの出力は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="aedcc-153">The program produces the following output:</span></span>

```console
Couse: Microeconomics, Student: Arturo Anand
Couse: Microeconomics, Student: Carson Bryant
```

## <a name="summary"></a><span data-ttu-id="aedcc-154">まとめ</span><span class="sxs-lookup"><span data-stu-id="aedcc-154">Summary</span></span>

<span data-ttu-id="aedcc-155">このチュートリアルでは、Entity Framework Designer を使用してテーブル値関数 (Tvf) をマップする方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="aedcc-155">In this walkthrough we looked at how to map Table-valued Functions (TVFs) using the Entity Framework Designer.</span></span> <span data-ttu-id="aedcc-156">また、LINQ クエリから TVF を呼び出す方法についても説明します。</span><span class="sxs-lookup"><span data-stu-id="aedcc-156">It also demonstrated how to call a TVF from a LINQ query.</span></span>
