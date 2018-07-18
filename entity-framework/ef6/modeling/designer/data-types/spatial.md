---
title: 空間の EF デザイナー - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 06baa6e1-d680-4a95-845b-81305c87a962
caps.latest.revision: 3
ms.openlocfilehash: 2332818275763fd90274f426b7fced4c3c14ac17
ms.sourcegitcommit: 390f3a37bc55105ed7cc5b0e0925b7f9c9e80ba6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2018
ms.locfileid: "39122555"
---
# <a name="spatial---ef-designer"></a><span data-ttu-id="0ecfd-102">空間の EF デザイナー</span><span class="sxs-lookup"><span data-stu-id="0ecfd-102">Spatial - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="0ecfd-103">**EF5 以降のみ**-機能、Api、Entity Framework 5 で導入されたなどのこのページで説明します。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-103">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="0ecfd-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="0ecfd-105">ビデオ、およびステップ バイ ステップ チュートリアルでは、Entity Framework デザイナーで空間型をマップする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-105">The video and step-by-step walkthrough shows how to map spatial types with the Entity Framework Designer.</span></span> <span data-ttu-id="0ecfd-106">LINQ クエリを使用して、2 つの場所間の距離を検索する方法も示します。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-106">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="0ecfd-107">このチュートリアルで、新しいデータベースを作成する最初のモデル使用しますが、EF デザイナーはでも使用できます、 [Database First](~/ef6/modeling/designer/workflows/database-first.md)ワークフローに既存のデータベースにマップします。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-107">This walkthrough will use Model First to create a new database, but the EF Designer can also be used with the [Database First](~/ef6/modeling/designer/workflows/database-first.md) workflow to map to an existing database.</span></span>

<span data-ttu-id="0ecfd-108">空間型のサポートは、Entity Framework 5 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-108">Spatial type support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="0ecfd-109">空間型、列挙型、およびテーブル値関数などの新機能を使用する .NET Framework 4.5 をターゲットする必要がありますに注意してください。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-109">Note that to use the new features like spatial type, enums, and Table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="0ecfd-110">Visual Studio 2012 では、既定では .NET 4.5 を対象とします。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-110">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="0ecfd-111">空間データ型を使用するには、空間をサポートする Entity Framework プロバイダーを使用することも必要があります。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-111">To use spatial data types you must also use an Entity Framework provider that has spatial support.</span></span> <span data-ttu-id="0ecfd-112">参照してください[空間型のプロバイダー サポート](~/ef6/fundamentals/providers/spatial-support.md)詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-112">See [provider support for spatial types](~/ef6/fundamentals/providers/spatial-support.md) for more information.</span></span>

<span data-ttu-id="0ecfd-113">2 つのメインの空間データの種類があります: geography および geometry します。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-113">There are two main spatial data types: geography and geometry.</span></span> <span data-ttu-id="0ecfd-114">Geography データ型の楕円体データを格納する (たとえば、GPS の緯度と経度座標)。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-114">The geography data type stores ellipsoidal data (for example, GPS latitude and longitude coordinates).</span></span> <span data-ttu-id="0ecfd-115">Geometry データ型は、ユークリッド (平面) 座標系を表します。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-115">The geometry data type represents Euclidean (flat) coordinate system.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="0ecfd-116">ビデオを見る</span><span class="sxs-lookup"><span data-stu-id="0ecfd-116">Watch the video</span></span>
<span data-ttu-id="0ecfd-117">このビデオでは、Entity Framework デザイナーで空間型をマップする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-117">This video shows how to map spatial types with the Entity Framework Designer.</span></span> <span data-ttu-id="0ecfd-118">LINQ クエリを使用して、2 つの場所間の距離を検索する方法も示します。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-118">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="0ecfd-119">**によって提示される**: Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="0ecfd-119">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="0ecfd-120">**ビデオ**: [WMV](http://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv) | [MP4](http://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)</span><span class="sxs-lookup"><span data-stu-id="0ecfd-120">**Video**: [WMV](http://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv) | [MP4](http://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="0ecfd-121">前提条件</span><span class="sxs-lookup"><span data-stu-id="0ecfd-121">Pre-Requisites</span></span>

<span data-ttu-id="0ecfd-122">このチュートリアルを完了にインストールされている Visual Studio 2012、Ultimate、Premium、Professional、または Web Express edition を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-122">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="0ecfd-123">プロジェクトを設定します。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-123">Set up the Project</span></span>

1.  <span data-ttu-id="0ecfd-124">Visual Studio 2012 を開く</span><span class="sxs-lookup"><span data-stu-id="0ecfd-124">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="0ecfd-125">**ファイル**メニューで、**新規**、 をクリックし、**プロジェクト**</span><span class="sxs-lookup"><span data-stu-id="0ecfd-125">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="0ecfd-126">左側のウィンドウで次のようにクリックします**Visual C\#** を選び、**コンソール**テンプレート。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-126">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="0ecfd-127">入力**SpatialEFDesigner**をクリックして、プロジェクトの名前として**OK**</span><span class="sxs-lookup"><span data-stu-id="0ecfd-127">Enter **SpatialEFDesigner** as the name of the project and click **OK**</span></span>

## <a name="create-a-new-model-using-the-ef-designer"></a><span data-ttu-id="0ecfd-128">EF デザイナーを使用して、新しいモデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-128">Create a New Model using the EF Designer</span></span>

1.  <span data-ttu-id="0ecfd-129">ソリューション エクスプ ローラーでプロジェクト名を右クリックし、[**追加**、] をクリックし、**新しい項目**</span><span class="sxs-lookup"><span data-stu-id="0ecfd-129">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="0ecfd-130">選択**データ**選択し、左側のメニューから**ADO.NET Entity Data Model**テンプレート ペイン</span><span class="sxs-lookup"><span data-stu-id="0ecfd-130">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane</span></span>
3.  <span data-ttu-id="0ecfd-131">入力**UniversityModel.edmx**のファイル名、およびクリック**追加**</span><span class="sxs-lookup"><span data-stu-id="0ecfd-131">Enter **UniversityModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="0ecfd-132">Entity Data Model ウィザード ページで、次のように選択します**空のモデル**モデルのコンテンツの選択 ダイアログ ボックス。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-132">On the Entity Data Model Wizard page, select **Empty Model** in the Choose Model Contents dialog box</span></span>
5.  <span data-ttu-id="0ecfd-133">クリックして**完了**</span><span class="sxs-lookup"><span data-stu-id="0ecfd-133">Click **Finish**</span></span>

<span data-ttu-id="0ecfd-134">モデルを編集するため、デザイン サーフェイスを提供するエンティティ デザイナーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-134">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

<span data-ttu-id="0ecfd-135">ウィザードは、次のアクションを実行します。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-135">The wizard performs the following actions:</span></span>

-   <span data-ttu-id="0ecfd-136">概念モデル、ストレージ モデル、およびそれらの間のマッピングを定義する EnumTestModel.edmx ファイルを生成します。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-136">Generates the EnumTestModel.edmx file that defines the conceptual model, the storage model, and the mapping between them.</span></span> <span data-ttu-id="0ecfd-137">生成されたメタデータ ファイルがアセンブリに埋め込まを取得するために、出力アセンブリに埋め込むに .edmx ファイルの Metadata Artifact Processing プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-137">Sets the Metadata Artifact Processing property of the .edmx file to Embed in Output Assembly so the generated metadata files get embedded into the assembly.</span></span>
-   <span data-ttu-id="0ecfd-138">次のアセンブリへの参照を追加します: EntityFramework、System.ComponentModel.DataAnnotations、System.Data.Entity します。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-138">Adds a reference to the following assemblies: EntityFramework, System.ComponentModel.DataAnnotations, and System.Data.Entity.</span></span>
-   <span data-ttu-id="0ecfd-139">UniversityModel.tt と UniversityModel.Context.tt ファイルを作成し、.edmx ファイルで追加します。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-139">Creates UniversityModel.tt and UniversityModel.Context.tt files and adds them under the .edmx file.</span></span> <span data-ttu-id="0ecfd-140">これらの T4 テンプレート ファイルは、DbContext 派生型と .edmx モデル内のエンティティにマップされる POCO 型を定義するコードを生成します。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-140">These T4 template files generate the code that defines the DbContext derived type and POCO types that map to the entities in the .edmx model</span></span>

## <a name="add-a-new-entity-type"></a><span data-ttu-id="0ecfd-141">新しいエンティティ型を追加します。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-141">Add a New Entity Type</span></span>

1.  <span data-ttu-id="0ecfd-142">デザイン画面の空の領域を右クリックして**追加 -&gt;エンティティ**、新しいエンティティのダイアログ ボックスが表示されます</span><span class="sxs-lookup"><span data-stu-id="0ecfd-142">Right-click an empty area of the design surface, select **Add -&gt; Entity**, the New Entity dialog box appears</span></span>
2.  <span data-ttu-id="0ecfd-143">指定**大学**型の名前を指定し、指定**UniversityID**キー プロパティの名前と型をそのまま使用**Int32**</span><span class="sxs-lookup"><span data-stu-id="0ecfd-143">Specify **University** for the type name and specify **UniversityID** for the key property name, leave the type as **Int32**</span></span>
3.  <span data-ttu-id="0ecfd-144">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-144">Click **OK**</span></span>
4.  <span data-ttu-id="0ecfd-145">エンティティを右クリックして **- 新しい追加&gt;スカラー プロパティ**</span><span class="sxs-lookup"><span data-stu-id="0ecfd-145">Right-click the entity and select **Add New -&gt; Scalar Property**</span></span>
5.  <span data-ttu-id="0ecfd-146">新しいプロパティの名前を変更**名**</span><span class="sxs-lookup"><span data-stu-id="0ecfd-146">Rename the new property to **Name**</span></span>
6.  <span data-ttu-id="0ecfd-147">もう 1 つのスカラー プロパティを追加し、名前を変更する**場所**プロパティ ウィンドウを開き、新しいプロパティの型を変更**Geography**</span><span class="sxs-lookup"><span data-stu-id="0ecfd-147">Add another scalar property and rename it to **Location** Open the Properties window and change the type of the new property to **Geography**</span></span>
7.  <span data-ttu-id="0ecfd-148">モデルを保存し、プロジェクトをビルド</span><span class="sxs-lookup"><span data-stu-id="0ecfd-148">Save the model and build the project</span></span>
    > [!NOTE]
    > <span data-ttu-id="0ecfd-149">ビルドすると、マップされていないエンティティとアソシエーションに関する警告がエラー一覧に表示されます。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-149">When you build, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="0ecfd-150">エラーが解消されますモデルからデータベースを生成するように選択した後にあるために、これらの警告を無視できます。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-150">You can ignore these warnings because after we choose to generate the database from the model, the errors will go away.</span></span>

## <a name="generate-database-from-model"></a><span data-ttu-id="0ecfd-151">モデルからデータベースを生成します。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-151">Generate Database from Model</span></span>

<span data-ttu-id="0ecfd-152">モデルに基づいているデータベースを生成できます。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-152">Now we can generate a database that is based on the model.</span></span>

1.  <span data-ttu-id="0ecfd-153">エンティティ デザイナー サーフェスと選択を空の領域を右クリックして**モデルからのデータベースの生成**</span><span class="sxs-lookup"><span data-stu-id="0ecfd-153">Right-click an empty space on the Entity Designer surface and select **Generate Database from Model**</span></span>
2.  <span data-ttu-id="0ecfd-154">データベースの生成ウィザードのデータ接続ダイアログ ボックスが選択の表示 をクリックして、**新しい接続**ボタンを指定する **(localdb)\\mssqllocaldb**サーバー名および**大学**、データベースとクリック**OK**</span><span class="sxs-lookup"><span data-stu-id="0ecfd-154">The Choose Your Data Connection Dialog Box of the Generate Database Wizard is displayed Click the **New Connection** button Specify **(localdb)\\mssqllocaldb** for the server name and **University** for the database and click **OK**</span></span>
3.  <span data-ttu-id="0ecfd-155">新しいデータベースを作成するかどうかたずねるダイアログがポップアップ表示をクリックします**はい**します。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-155">A dialog asking if you want to create a new database will pop up, click **Yes**.</span></span>
4.  <span data-ttu-id="0ecfd-156">をクリックして**次**概要、および DDL の定義が含まれていないの設定 ダイアログ ボックス注に表示されますが、データベースを作成するには、生成された DDL をデータベースの作成ウィザードがデータ定義言語 (DDL) を生成し、列挙型にマップされるテーブル</span><span class="sxs-lookup"><span data-stu-id="0ecfd-156">Click **Next** and the Create Database Wizard generates data definition language (DDL) for creating a database The generated DDL is displayed in the Summary and Settings Dialog Box Note, that the DDL does not contain a definition for a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="0ecfd-157">クリックして**完了**完了 をクリックしても、DDL スクリプトは実行されません。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-157">Click **Finish** Clicking Finish does not execute the DDL script.</span></span>
6.  <span data-ttu-id="0ecfd-158">データベースの作成ウィザードは、次: 開きます、 **UniversityModel.edmx.sql** T-SQL エディターの生成で EDMX のストアのスキーマとマッピング セクションでは App.config ファイルに追加の接続文字列情報をファイル</span><span class="sxs-lookup"><span data-stu-id="0ecfd-158">The Create Database Wizard does the following: Opens the **UniversityModel.edmx.sql** in T-SQL Editor Generates the store schema and mapping sections of the EDMX file Adds connection string information to the App.config file</span></span>
7.  <span data-ttu-id="0ecfd-159">T-SQL エディターでマウスの右ボタンをクリックし、選択**Execute**サーバー ダイアログへの接続が表示されたら、手順 2. の接続情報を入力し、クリックして**接続**</span><span class="sxs-lookup"><span data-stu-id="0ecfd-159">Click the right mouse button in T-SQL Editor and select **Execute** The Connect to Server dialog appears, enter the connection information from step 2 and click **Connect**</span></span>
8.  <span data-ttu-id="0ecfd-160">生成されたスキーマを表示するには、SQL Server オブジェクト エクスプ ローラーでデータベース名を右クリックして**更新**</span><span class="sxs-lookup"><span data-stu-id="0ecfd-160">To view the generated schema, right-click on the database name in SQL Server Object Explorer and select **Refresh**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="0ecfd-161">永続化およびデータの取得</span><span class="sxs-lookup"><span data-stu-id="0ecfd-161">Persist and Retrieve Data</span></span>

<span data-ttu-id="0ecfd-162">Main メソッドが定義されている Program.cs ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-162">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="0ecfd-163">Main 関数には、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-163">Add the following code into the Main function.</span></span>

<span data-ttu-id="0ecfd-164">コードでは、コンテキストに新しい大学の 2 つのオブジェクトを追加します。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-164">The code adds two new University objects to the context.</span></span> <span data-ttu-id="0ecfd-165">空間プロパティは、DbGeography.FromText メソッドを使用して初期化されます。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-165">Spatial properties are initialized by using the DbGeography.FromText method.</span></span> <span data-ttu-id="0ecfd-166">地理的ポイント WellKnownText がメソッドに渡されるとして表されます。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-166">The geography point represented as WellKnownText is passed to the method.</span></span> <span data-ttu-id="0ecfd-167">コードは、データを保存します。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-167">The code then saves the data.</span></span> <span data-ttu-id="0ecfd-168">次に、LINQ クエリでその場所が指定した場所に最も近い大学オブジェクトを返す構成および実行します。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-168">Then, the LINQ query that that returns a University object where its location is closest to the specified location, is constructed and executed.</span></span>

``` csharp
using (var context = new UniversityModelContainer())
{
    context.Universities.Add(new University()
    {
        Name = "Graphic Design Institute",
        Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
    });

    context.Universities.Add(new University()
    {
        Name = "School of Fine Art",
        Location = DbGeography.FromText("POINT(-122.335197 47.646711)"),
    });

    context.SaveChanges();

    var myLocation = DbGeography.FromText("POINT(-122.296623 47.640405)");

    var university = (from u in context.Universities
                                orderby u.Location.Distance(myLocation)
                                select u).FirstOrDefault();

    Console.WriteLine(
        "The closest University to you is: {0}.",
        university.Name);
}
```

<span data-ttu-id="0ecfd-169">アプリケーションをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-169">Compile and run the application.</span></span> <span data-ttu-id="0ecfd-170">このプログラムの出力は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-170">The program produces the following output:</span></span>

```
The closest University to you is: School of Fine Art.
```

<span data-ttu-id="0ecfd-171">データベースにデータを表示するには、SQL Server オブジェクト エクスプ ローラーでデータベース名を右クリックして**更新**します。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-171">To view data in the database, right-click on the database name in SQL Server Object Explorer and select **Refresh**.</span></span> <span data-ttu-id="0ecfd-172">マウスの右ボタンをクリックし、テーブルをクリックし、**ビュー データ**します。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-172">Then, click the right mouse button on the table and select **View Data**.</span></span>

## <a name="summary"></a><span data-ttu-id="0ecfd-173">まとめ</span><span class="sxs-lookup"><span data-stu-id="0ecfd-173">Summary</span></span>

<span data-ttu-id="0ecfd-174">このチュートリアルでは、Entity Framework デザイナーを使用して空間型をマップする方法とコードで空間型を使用する方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="0ecfd-174">In this walkthrough we looked at how to map spatial types using the Entity Framework Designer and how to use spatial types in code.</span></span> 
