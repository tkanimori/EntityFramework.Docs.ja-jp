---
title: 空間-EF デザイナー-EF6
description: Entity Framework 6 の空間 EF デザイナー
author: divega
ms.date: 10/23/2016
ms.assetid: 06baa6e1-d680-4a95-845b-81305c87a962
uid: ef6/modeling/designer/data-types/spatial
ms.openlocfilehash: 10430d6a043182e5536dfad148dc4158cf4c96c9
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620490"
---
# <a name="spatial---ef-designer"></a><span data-ttu-id="905db-103">空間-EF デザイナー</span><span class="sxs-lookup"><span data-stu-id="905db-103">Spatial - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="905db-104">**EF5** 以降: このページで説明した機能、api などは、Entity Framework 5 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="905db-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="905db-105">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="905db-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="905db-106">ビデオとステップバイステップのチュートリアルでは、空間型を Entity Framework Designer にマップする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="905db-106">The video and step-by-step walkthrough shows how to map spatial types with the Entity Framework Designer.</span></span> <span data-ttu-id="905db-107">また、LINQ クエリを使用して2つの場所の間の距離を検索する方法も示します。</span><span class="sxs-lookup"><span data-stu-id="905db-107">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="905db-108">このチュートリアルでは Model First を使用して新しいデータベースを作成しますが、既存のデータベースにマップするには、EF Designer を [Database First](xref:ef6/modeling/designer/workflows/database-first) ワークフローと共に使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="905db-108">This walkthrough will use Model First to create a new database, but the EF Designer can also be used with the [Database First](xref:ef6/modeling/designer/workflows/database-first) workflow to map to an existing database.</span></span>

<span data-ttu-id="905db-109">空間型のサポートは Entity Framework 5 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="905db-109">Spatial type support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="905db-110">空間型、列挙型、テーブル値関数などの新機能を使用するには、.NET Framework 4.5 をターゲットにする必要があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="905db-110">Note that to use the new features like spatial type, enums, and Table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="905db-111">既定では、Visual Studio 2012 は .NET 4.5 を対象としています。</span><span class="sxs-lookup"><span data-stu-id="905db-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="905db-112">空間データ型を使用するには、空間をサポートする Entity Framework プロバイダーも使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="905db-112">To use spatial data types you must also use an Entity Framework provider that has spatial support.</span></span> <span data-ttu-id="905db-113">詳細については、「 [プロバイダーによる空間型のサポート](xref:ef6/fundamentals/providers/spatial-support) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="905db-113">See [provider support for spatial types](xref:ef6/fundamentals/providers/spatial-support) for more information.</span></span>

<span data-ttu-id="905db-114">主な空間データ型には、geography と geometry の2つがあります。</span><span class="sxs-lookup"><span data-stu-id="905db-114">There are two main spatial data types: geography and geometry.</span></span> <span data-ttu-id="905db-115">Geography データ型には、楕円体データ (GPS の緯度や経度の座標など) が格納されます。</span><span class="sxs-lookup"><span data-stu-id="905db-115">The geography data type stores ellipsoidal data (for example, GPS latitude and longitude coordinates).</span></span> <span data-ttu-id="905db-116">Geometry データ型は、ユークリッド (平面) 座標系を表します。</span><span class="sxs-lookup"><span data-stu-id="905db-116">The geometry data type represents Euclidean (flat) coordinate system.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="905db-117">ビデオを見る</span><span class="sxs-lookup"><span data-stu-id="905db-117">Watch the video</span></span>
<span data-ttu-id="905db-118">このビデオでは、空間型を Entity Framework Designer にマップする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="905db-118">This video shows how to map spatial types with the Entity Framework Designer.</span></span> <span data-ttu-id="905db-119">また、LINQ クエリを使用して2つの場所の間の距離を検索する方法も示します。</span><span class="sxs-lookup"><span data-stu-id="905db-119">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="905db-120">**表示者**: ジュリアつい</span><span class="sxs-lookup"><span data-stu-id="905db-120">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="905db-121">**ビデオ**: [WMV](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv)  |  [MP4](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v)  |  [WMV (ZIP)](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)</span><span class="sxs-lookup"><span data-stu-id="905db-121">**Video**: [WMV](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv) | [MP4](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="905db-122">前提条件</span><span class="sxs-lookup"><span data-stu-id="905db-122">Pre-Requisites</span></span>

<span data-ttu-id="905db-123">このチュートリアルを完了するには、Visual Studio 2012、Ultimate、Premium、Professional、または Web Express エディションがインストールされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="905db-123">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="905db-124">プロジェクトを設定する</span><span class="sxs-lookup"><span data-stu-id="905db-124">Set up the Project</span></span>

1.  <span data-ttu-id="905db-125">Visual Studio 2012 を開く</span><span class="sxs-lookup"><span data-stu-id="905db-125">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="905db-126">[**ファイル**] メニューの [**新規作成**] をポイントし、[**プロジェクト**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="905db-126">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="905db-127">左側のウィンドウで、[ **Visual C \# **] をクリックし、**コンソール**テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="905db-127">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="905db-128">プロジェクトの名前として「 **SpatialEFDesigner** 」と入力し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="905db-128">Enter **SpatialEFDesigner** as the name of the project and click **OK**</span></span>

## <a name="create-a-new-model-using-the-ef-designer"></a><span data-ttu-id="905db-129">EF デザイナーを使用して新しいモデルを作成する</span><span class="sxs-lookup"><span data-stu-id="905db-129">Create a New Model using the EF Designer</span></span>

1.  <span data-ttu-id="905db-130">ソリューションエクスプローラーでプロジェクト名を右クリックし、[**追加**] をポイントして、[**新しい項目**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="905db-130">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="905db-131">左側のメニューから [ **データ** ] を選択し、[テンプレート] ペインで [ **ADO.NET Entity Data Model** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="905db-131">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane</span></span>
3.  <span data-ttu-id="905db-132">ファイル名として「UniversityModel」と入力し、[**追加**] をクリックし**ます。**</span><span class="sxs-lookup"><span data-stu-id="905db-132">Enter **UniversityModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="905db-133">Entity Data Model ウィザードページで、[モデルのコンテンツの選択] ダイアログボックスの [ **空のモデル** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="905db-133">On the Entity Data Model Wizard page, select **Empty Model** in the Choose Model Contents dialog box</span></span>
5.  <span data-ttu-id="905db-134">**[完了]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="905db-134">Click **Finish**</span></span>

<span data-ttu-id="905db-135">モデルを編集するためのデザイン画面を提供する Entity Designer が表示されます。</span><span class="sxs-lookup"><span data-stu-id="905db-135">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

<span data-ttu-id="905db-136">ウィザードは、次のアクションを実行します。</span><span class="sxs-lookup"><span data-stu-id="905db-136">The wizard performs the following actions:</span></span>

-   <span data-ttu-id="905db-137">概念モデル、ストレージモデル、およびそれらの間のマッピングを定義する EnumTestModel .edmx ファイルを生成します。</span><span class="sxs-lookup"><span data-stu-id="905db-137">Generates the EnumTestModel.edmx file that defines the conceptual model, the storage model, and the mapping between them.</span></span> <span data-ttu-id="905db-138">生成されたメタデータファイルがアセンブリに埋め込まれるように、出力アセンブリに埋め込む .edmx ファイルのメタデータ成果物の処理プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="905db-138">Sets the Metadata Artifact Processing property of the .edmx file to Embed in Output Assembly so the generated metadata files get embedded into the assembly.</span></span>
-   <span data-ttu-id="905db-139">次のアセンブリへの参照を追加します。 EntityFramework、System.componentmodel、および system.object です。</span><span class="sxs-lookup"><span data-stu-id="905db-139">Adds a reference to the following assemblies: EntityFramework, System.ComponentModel.DataAnnotations, and System.Data.Entity.</span></span>
-   <span data-ttu-id="905db-140">UniversityModel.tt ファイルと UniversityModel.Context.tt ファイルを作成し、.edmx ファイルの下に追加します。</span><span class="sxs-lookup"><span data-stu-id="905db-140">Creates UniversityModel.tt and UniversityModel.Context.tt files and adds them under the .edmx file.</span></span> <span data-ttu-id="905db-141">これらの T4 テンプレートファイルによって、.edmx モデル内のエンティティにマップされる DbContext の派生型と POCO 型を定義するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="905db-141">These T4 template files generate the code that defines the DbContext derived type and POCO types that map to the entities in the .edmx model</span></span>

## <a name="add-a-new-entity-type"></a><span data-ttu-id="905db-142">新しいエンティティ型の追加</span><span class="sxs-lookup"><span data-stu-id="905db-142">Add a New Entity Type</span></span>

1.  <span data-ttu-id="905db-143">デザイン画面の空の領域を右クリックし、[ \*\* &gt; エンティティの追加\*\*] を選択すると、[新しいエンティティ] ダイアログボックスが表示されます。</span><span class="sxs-lookup"><span data-stu-id="905db-143">Right-click an empty area of the design surface, select **Add -&gt; Entity**, the New Entity dialog box appears</span></span>
2.  <span data-ttu-id="905db-144">型名として「**大学**」を指定し、キープロパティ名として「 **UniversityID** 」を指定し、型を**Int32**にします。</span><span class="sxs-lookup"><span data-stu-id="905db-144">Specify **University** for the type name and specify **UniversityID** for the key property name, leave the type as **Int32**</span></span>
3.  <span data-ttu-id="905db-145">**[OK]**</span><span class="sxs-lookup"><span data-stu-id="905db-145">Click **OK**</span></span>
4.  <span data-ttu-id="905db-146">エンティティを右クリックし、[**新しい &gt; スカラープロパティの追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="905db-146">Right-click the entity and select **Add New -&gt; Scalar Property**</span></span>
5.  <span data-ttu-id="905db-147">新しいプロパティの名前を**Name**に変更します。</span><span class="sxs-lookup"><span data-stu-id="905db-147">Rename the new property to **Name**</span></span>
6.  <span data-ttu-id="905db-148">別のスカラープロパティを追加し、その名前を「 **Location** 」に変更して、プロパティウィンドウを開き、新しいプロパティの型を**Geography**に変更します。</span><span class="sxs-lookup"><span data-stu-id="905db-148">Add another scalar property and rename it to **Location** Open the Properties window and change the type of the new property to **Geography**</span></span>
7.  <span data-ttu-id="905db-149">モデルを保存してプロジェクトをビルドする</span><span class="sxs-lookup"><span data-stu-id="905db-149">Save the model and build the project</span></span>
    > [!NOTE]
    > <span data-ttu-id="905db-150">ビルドすると、マップされていないエンティティとアソシエーションに関する警告がエラー一覧に表示されることがあります。</span><span class="sxs-lookup"><span data-stu-id="905db-150">When you build, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="905db-151">モデルからデータベースを生成することを選択した後、エラーが解消されるため、これらの警告は無視してかまいません。</span><span class="sxs-lookup"><span data-stu-id="905db-151">You can ignore these warnings because after we choose to generate the database from the model, the errors will go away.</span></span>

## <a name="generate-database-from-model"></a><span data-ttu-id="905db-152">モデルからデータベースを生成</span><span class="sxs-lookup"><span data-stu-id="905db-152">Generate Database from Model</span></span>

<span data-ttu-id="905db-153">これで、モデルに基づくデータベースを生成できます。</span><span class="sxs-lookup"><span data-stu-id="905db-153">Now we can generate a database that is based on the model.</span></span>

1.  <span data-ttu-id="905db-154">Entity Designer 画面の空の領域を右クリックし、[**モデルからデータベースを生成**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="905db-154">Right-click an empty space on the Entity Designer surface and select **Generate Database from Model**</span></span>
2.  <span data-ttu-id="905db-155">データベース生成ウィザードの [データ接続の選択] ダイアログボックスが表示されます。 [**新しい接続**] ボタンをクリックして、データベースのサーバー名と**大学**の\*\* \\ mssqllocaldb (localdb)\*\* を指定し、[ **OK** ] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="905db-155">The Choose Your Data Connection Dialog Box of the Generate Database Wizard is displayed Click the **New Connection** button Specify **(localdb)\\mssqllocaldb** for the server name and **University** for the database and click **OK**</span></span>
3.  <span data-ttu-id="905db-156">新しいデータベースを作成するかどうかを確認するダイアログボックスが表示されたら、[ **はい**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="905db-156">A dialog asking if you want to create a new database will pop up, click **Yes**.</span></span>
4.  <span data-ttu-id="905db-157">[ **次へ** ] をクリックすると、データベースの作成ウィザードによって、生成された ddl がデータベースを作成するためのデータ定義言語 (ddl) が生成されます。 [概要と設定] ダイアログボックスには、ddl に列挙型にマップされているテーブルの定義が含まれていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="905db-157">Click **Next** and the Create Database Wizard generates data definition language (DDL) for creating a database The generated DDL is displayed in the Summary and Settings Dialog Box Note, that the DDL does not contain a definition for a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="905db-158">[ **完了** ] をクリックすると、DDL スクリプトは実行されません。</span><span class="sxs-lookup"><span data-stu-id="905db-158">Click **Finish** Clicking Finish does not execute the DDL script.</span></span>
6.  <span data-ttu-id="905db-159">データベースの作成ウィザードでは、次のことが行われます。 T-sql エディターで **UniversityModel** が生成されると、edmx ファイルのストアスキーマとマッピングセクションによって、接続文字列情報が App.config ファイルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="905db-159">The Create Database Wizard does the following: Opens the **UniversityModel.edmx.sql** in T-SQL Editor Generates the store schema and mapping sections of the EDMX file Adds connection string information to the App.config file</span></span>
7.  <span data-ttu-id="905db-160">T-sql エディターでマウスの右ボタンをクリック**し、[サーバーへの接続**] ダイアログボックスが表示されたら、手順2の接続情報を入力して [**接続**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="905db-160">Click the right mouse button in T-SQL Editor and select **Execute** The Connect to Server dialog appears, enter the connection information from step 2 and click **Connect**</span></span>
8.  <span data-ttu-id="905db-161">生成されたスキーマを表示するには、SQL Server オブジェクトエクスプローラーでデータベース名を右クリックし、[最新の状態に**更新**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="905db-161">To view the generated schema, right-click on the database name in SQL Server Object Explorer and select **Refresh**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="905db-162">データの永続化と取得</span><span class="sxs-lookup"><span data-stu-id="905db-162">Persist and Retrieve Data</span></span>

<span data-ttu-id="905db-163">Main メソッドが定義されている Program.cs ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="905db-163">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="905db-164">Main 関数に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="905db-164">Add the following code into the Main function.</span></span>

<span data-ttu-id="905db-165">このコードでは、2つの新しい大学オブジェクトをコンテキストに追加します。</span><span class="sxs-lookup"><span data-stu-id="905db-165">The code adds two new University objects to the context.</span></span> <span data-ttu-id="905db-166">空間プロパティは、FromText メソッドを使用して初期化されます。</span><span class="sxs-lookup"><span data-stu-id="905db-166">Spatial properties are initialized by using the DbGeography.FromText method.</span></span> <span data-ttu-id="905db-167">WellKnownText として表される geography ポイントがメソッドに渡されます。</span><span class="sxs-lookup"><span data-stu-id="905db-167">The geography point represented as WellKnownText is passed to the method.</span></span> <span data-ttu-id="905db-168">次に、データを保存します。</span><span class="sxs-lookup"><span data-stu-id="905db-168">The code then saves the data.</span></span> <span data-ttu-id="905db-169">次に、指定した場所に最も近い大学オブジェクトを返す LINQ クエリを構築して実行します。</span><span class="sxs-lookup"><span data-stu-id="905db-169">Then, the LINQ query that that returns a University object where its location is closest to the specified location, is constructed and executed.</span></span>

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

<span data-ttu-id="905db-170">アプリケーションをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="905db-170">Compile and run the application.</span></span> <span data-ttu-id="905db-171">このプログラムの出力は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="905db-171">The program produces the following output:</span></span>

```console
The closest University to you is: School of Fine Art.
```

<span data-ttu-id="905db-172">データベースのデータを表示するには、SQL Server オブジェクトエクスプローラーでデータベース名を右クリックし、[最新の情報に **更新**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="905db-172">To view data in the database, right-click on the database name in SQL Server Object Explorer and select **Refresh**.</span></span> <span data-ttu-id="905db-173">次に、テーブルのマウスの右ボタンをクリックし、[ **データの表示**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="905db-173">Then, click the right mouse button on the table and select **View Data**.</span></span>

## <a name="summary"></a><span data-ttu-id="905db-174">要約</span><span class="sxs-lookup"><span data-stu-id="905db-174">Summary</span></span>

<span data-ttu-id="905db-175">このチュートリアルでは、Entity Framework Designer を使用して空間型をマップする方法と、コードで空間型を使用する方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="905db-175">In this walkthrough we looked at how to map spatial types using the Entity Framework Designer and how to use spatial types in code.</span></span> 
