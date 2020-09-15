---
title: 空間 Code First-EF6
description: Entity Framework 6 の空間 Code First
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/data-types/spatial
ms.openlocfilehash: 5d18624cdcef8db3e604277aeef1c3a2d05bfcab
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073965"
---
# <a name="spatial---code-first"></a><span data-ttu-id="b39e4-103">空間 Code First</span><span class="sxs-lookup"><span data-stu-id="b39e4-103">Spatial - Code First</span></span>
> [!NOTE]
> <span data-ttu-id="b39e4-104">**EF5** 以降: このページで説明した機能、api などは、Entity Framework 5 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="b39e4-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="b39e4-105">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="b39e4-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="b39e4-106">ビデオとステップバイステップのチュートリアルでは、空間型を Entity Framework Code First にマップする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="b39e4-106">The video and step-by-step walkthrough shows how to map spatial types with Entity Framework Code First.</span></span> <span data-ttu-id="b39e4-107">また、LINQ クエリを使用して2つの場所の間の距離を検索する方法も示します。</span><span class="sxs-lookup"><span data-stu-id="b39e4-107">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="b39e4-108">このチュートリアルでは Code First を使用して新しいデータベースを作成しますが、 [既存のデータベースに Code First を](xref:ef6/modeling/code-first/workflows/existing-database)使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="b39e4-108">This walkthrough will use Code First to create a new database, but you can also use [Code First to an existing database](xref:ef6/modeling/code-first/workflows/existing-database).</span></span>

<span data-ttu-id="b39e4-109">空間型のサポートは Entity Framework 5 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="b39e4-109">Spatial type support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="b39e4-110">空間型、列挙型、テーブル値関数などの新機能を使用するには、.NET Framework 4.5 をターゲットにする必要があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="b39e4-110">Note that to use the new features like spatial type, enums, and Table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="b39e4-111">既定では、Visual Studio 2012 は .NET 4.5 を対象としています。</span><span class="sxs-lookup"><span data-stu-id="b39e4-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="b39e4-112">空間データ型を使用するには、空間をサポートする Entity Framework プロバイダーも使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b39e4-112">To use spatial data types you must also use an Entity Framework provider that has spatial support.</span></span> <span data-ttu-id="b39e4-113">詳細については、「 [プロバイダーによる空間型のサポート](xref:ef6/fundamentals/providers/spatial-support) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b39e4-113">See [provider support for spatial types](xref:ef6/fundamentals/providers/spatial-support) for more information.</span></span>

<span data-ttu-id="b39e4-114">主な空間データ型には、geography と geometry の2つがあります。</span><span class="sxs-lookup"><span data-stu-id="b39e4-114">There are two main spatial data types: geography and geometry.</span></span> <span data-ttu-id="b39e4-115">Geography データ型には、楕円体データ (GPS の緯度や経度の座標など) が格納されます。</span><span class="sxs-lookup"><span data-stu-id="b39e4-115">The geography data type stores ellipsoidal data (for example, GPS latitude and longitude coordinates).</span></span> <span data-ttu-id="b39e4-116">Geometry データ型は、ユークリッド (平面) 座標系を表します。</span><span class="sxs-lookup"><span data-stu-id="b39e4-116">The geometry data type represents Euclidean (flat) coordinate system.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="b39e4-117">ビデオを見る</span><span class="sxs-lookup"><span data-stu-id="b39e4-117">Watch the video</span></span>
<span data-ttu-id="b39e4-118">このビデオでは、空間型を Entity Framework Code First にマップする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="b39e4-118">This video shows how to map spatial types with Entity Framework Code First.</span></span> <span data-ttu-id="b39e4-119">また、LINQ クエリを使用して2つの場所の間の距離を検索する方法も示します。</span><span class="sxs-lookup"><span data-stu-id="b39e4-119">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="b39e4-120">**表示者**: ジュリアつい</span><span class="sxs-lookup"><span data-stu-id="b39e4-120">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="b39e4-121">**ビデオ**: [WMV](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.wmv)  |  [MP4](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-mp4video-spatialwithcodefirst.m4v)  |  [WMV (ZIP)](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.zip)</span><span class="sxs-lookup"><span data-stu-id="b39e4-121">**Video**: [WMV](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.wmv) | [MP4](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-mp4video-spatialwithcodefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="b39e4-122">前提条件</span><span class="sxs-lookup"><span data-stu-id="b39e4-122">Pre-Requisites</span></span>

<span data-ttu-id="b39e4-123">このチュートリアルを完了するには、Visual Studio 2012、Ultimate、Premium、Professional、または Web Express エディションがインストールされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="b39e4-123">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="b39e4-124">プロジェクトを設定する</span><span class="sxs-lookup"><span data-stu-id="b39e4-124">Set up the Project</span></span>

1.  <span data-ttu-id="b39e4-125">Visual Studio 2012 を開く</span><span class="sxs-lookup"><span data-stu-id="b39e4-125">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="b39e4-126">[**ファイル**] メニューの [**新規作成**] をポイントし、[**プロジェクト**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b39e4-126">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="b39e4-127">左側のウィンドウで、[ **Visual C \# **] をクリックし、**コンソール**テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="b39e4-127">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="b39e4-128">プロジェクトの名前として「 **SpatialCodeFirst** 」と入力し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b39e4-128">Enter **SpatialCodeFirst** as the name of the project and click **OK**</span></span>

## <a name="define-a-new-model-using-code-first"></a><span data-ttu-id="b39e4-129">Code First を使用して新しいモデルを定義する</span><span class="sxs-lookup"><span data-stu-id="b39e4-129">Define a New Model using Code First</span></span>

<span data-ttu-id="b39e4-130">Code First 開発を使用する場合は、通常、概念 (ドメイン) モデルを定義する .NET Framework クラスを記述することから始めます。</span><span class="sxs-lookup"><span data-stu-id="b39e4-130">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span> <span data-ttu-id="b39e4-131">次のコードは、大学クラスを定義しています。</span><span class="sxs-lookup"><span data-stu-id="b39e4-131">The code below defines the University class.</span></span>

<span data-ttu-id="b39e4-132">この大学は、DbGeography 型の Location プロパティを持っています。</span><span class="sxs-lookup"><span data-stu-id="b39e4-132">The University has the Location property of the DbGeography type.</span></span> <span data-ttu-id="b39e4-133">DbGeography 型を使用するには、system.string アセンブリへの参照を追加し、さらに、system.string using ステートメントも追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b39e4-133">To use the DbGeography type, you must add a reference to the System.Data.Entity assembly and also add the System.Data.Spatial using statement.</span></span>

<span data-ttu-id="b39e4-134">Program.cs ファイルを開き、次の using ステートメントをファイルの先頭に貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="b39e4-134">Open the Program.cs file and paste the following using statements at the top of the file:</span></span>

``` csharp
using System.Data.Spatial;
```

<span data-ttu-id="b39e4-135">次の大学クラス定義を Program.cs ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="b39e4-135">Add the following University class definition to the Program.cs file.</span></span>

``` csharp
public class University  
{
    public int UniversityID { get; set; }
    public string Name { get; set; }
    public DbGeography Location { get; set; }
}
```

## <a name="define-the-dbcontext-derived-type"></a><span data-ttu-id="b39e4-136">DbContext の派生型を定義する</span><span class="sxs-lookup"><span data-stu-id="b39e4-136">Define the DbContext Derived Type</span></span>

<span data-ttu-id="b39e4-137">エンティティを定義するだけでなく、DbContext から派生して DbSet&lt;TEntity&gt; プロパティを公開するクラスを定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b39e4-137">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="b39e4-138">DbSet&lt;TEntity&gt; プロパティにより、モデルに含める型をコンテキストに認識させることができます。</span><span class="sxs-lookup"><span data-stu-id="b39e4-138">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="b39e4-139">DbContext の派生型のインスタンスによって、実行時にエンティティ オブジェクトが管理されます。これには、オブジェクトへのデータベースのデータの設定、変更の追跡、データベースへのデータの保持が含まれます。</span><span class="sxs-lookup"><span data-stu-id="b39e4-139">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="b39e4-140">DbContext および Dbcontext 型は EntityFramework アセンブリで定義されています。</span><span class="sxs-lookup"><span data-stu-id="b39e4-140">The DbContext and DbSet types are defined in the EntityFramework assembly.</span></span> <span data-ttu-id="b39e4-141">ここでは、EntityFramework NuGet パッケージを使用して、この DLL への参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="b39e4-141">We will add a reference to this DLL by using the EntityFramework NuGet package.</span></span>

1.  <span data-ttu-id="b39e4-142">ソリューションエクスプローラーで、プロジェクト名を右クリックします。</span><span class="sxs-lookup"><span data-stu-id="b39e4-142">In Solution Explorer, right-click on the project name.</span></span>
2.  <span data-ttu-id="b39e4-143">[ **NuGet パッケージの管理...** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="b39e4-143">Select **Manage NuGet Packages…**</span></span>
3.  <span data-ttu-id="b39e4-144">[NuGet パッケージの管理] ダイアログで、[ **オンライン** ] タブを選択し、 **entityframework** パッケージを選択します。</span><span class="sxs-lookup"><span data-stu-id="b39e4-144">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package.</span></span>
4.  <span data-ttu-id="b39e4-145">**[Install]** (インストール) をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b39e4-145">Click **Install**</span></span>

<span data-ttu-id="b39e4-146">EntityFramework アセンブリに加えて、System.componentmodel アセンブリへの参照も追加されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="b39e4-146">Note, that in addition to the EntityFramework  assembly, a reference to the System.ComponentModel.DataAnnotations assembly is also added.</span></span>

<span data-ttu-id="b39e4-147">Program.cs ファイルの先頭に、次の using ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="b39e4-147">At the top of the Program.cs file, add the following using statement:</span></span>

``` csharp
using System.Data.Entity;
```

<span data-ttu-id="b39e4-148">Program.cs で、コンテキスト定義を追加します。</span><span class="sxs-lookup"><span data-stu-id="b39e4-148">In the Program.cs add the context definition.</span></span> 

``` csharp
public partial class UniversityContext : DbContext
{
    public DbSet<University> Universities { get; set; }
}
```

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="b39e4-149">データの永続化と取得</span><span class="sxs-lookup"><span data-stu-id="b39e4-149">Persist and Retrieve Data</span></span>

<span data-ttu-id="b39e4-150">Main メソッドが定義されている Program.cs ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="b39e4-150">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="b39e4-151">Main 関数に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="b39e4-151">Add the following code into the Main function.</span></span>

<span data-ttu-id="b39e4-152">このコードでは、2つの新しい大学オブジェクトをコンテキストに追加します。</span><span class="sxs-lookup"><span data-stu-id="b39e4-152">The code adds two new University objects to the context.</span></span> <span data-ttu-id="b39e4-153">空間プロパティは、FromText メソッドを使用して初期化されます。</span><span class="sxs-lookup"><span data-stu-id="b39e4-153">Spatial properties are initialized by using the DbGeography.FromText method.</span></span> <span data-ttu-id="b39e4-154">WellKnownText として表される geography ポイントがメソッドに渡されます。</span><span class="sxs-lookup"><span data-stu-id="b39e4-154">The geography point represented as WellKnownText is passed to the method.</span></span> <span data-ttu-id="b39e4-155">次に、データを保存します。</span><span class="sxs-lookup"><span data-stu-id="b39e4-155">The code then saves the data.</span></span> <span data-ttu-id="b39e4-156">次に、指定した場所に最も近い大学オブジェクトを返す LINQ クエリを構築して実行します。</span><span class="sxs-lookup"><span data-stu-id="b39e4-156">Then, the LINQ query that that returns a University object where its location is closest to the specified location, is constructed and executed.</span></span>

``` csharp
using (var context = new UniversityContext ())
{
    context.Universities.Add(new University()
        {
            Name = "Graphic Design Institute",
            Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
        });

    context. Universities.Add(new University()
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

<span data-ttu-id="b39e4-157">アプリケーションをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="b39e4-157">Compile and run the application.</span></span> <span data-ttu-id="b39e4-158">このプログラムの出力は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b39e4-158">The program produces the following output:</span></span>

```console
The closest University to you is: School of Fine Art.
```

## <a name="view-the-generated-database"></a><span data-ttu-id="b39e4-159">生成されたデータベースを表示する</span><span class="sxs-lookup"><span data-stu-id="b39e4-159">View the Generated Database</span></span>

<span data-ttu-id="b39e4-160">アプリケーションを初めて実行すると、Entity Framework によってデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b39e4-160">When you run the application the first time, the Entity Framework creates a database for you.</span></span> <span data-ttu-id="b39e4-161">Visual Studio 2012 がインストールされているため、データベースは LocalDB インスタンス上に作成されます。</span><span class="sxs-lookup"><span data-stu-id="b39e4-161">Because we have Visual Studio 2012 installed, the database will be created on the LocalDB instance.</span></span> <span data-ttu-id="b39e4-162">既定では、Entity Framework は、派生コンテキストの完全修飾名 (この例では **UniversityContext**) の後にデータベースに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b39e4-162">By default, the Entity Framework names the database after the fully qualified name of the derived context (in this example that is **SpatialCodeFirst.UniversityContext**).</span></span> <span data-ttu-id="b39e4-163">その後、既存のデータベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="b39e4-163">The subsequent times the existing database will be used.</span></span>  

<span data-ttu-id="b39e4-164">データベースの作成後にモデルに変更を加えた場合は、Code First Migrations を使用してデータベーススキーマを更新する必要があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="b39e4-164">Note, that if you make any changes to your model after the database has been created, you should use Code First Migrations to update the database schema.</span></span> <span data-ttu-id="b39e4-165">移行の使用例については [、「新しいデータベースへの Code First](xref:ef6/modeling/code-first/workflows/new-database) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b39e4-165">See [Code First to a New Database](xref:ef6/modeling/code-first/workflows/new-database) for an example of using Migrations.</span></span>

<span data-ttu-id="b39e4-166">データベースとデータを表示するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="b39e4-166">To view the database and data, do the following:</span></span>

1.  <span data-ttu-id="b39e4-167">Visual Studio 2012 のメインメニューで、[SQL Server オブジェクトエクスプローラーの**表示**] を選択し  - &gt; **SQL Server Object Explorer**ます。</span><span class="sxs-lookup"><span data-stu-id="b39e4-167">In the Visual Studio 2012 main menu, select **View** -&gt; **SQL Server Object Explorer**.</span></span>
2.  <span data-ttu-id="b39e4-168">LocalDB がサーバーの一覧にない場合は、 **SQL Server** で右マウスボタンをクリックし、[追加] を選択 **SQL Server** 既定の **Windows 認証** を使用して localdb インスタンスに接続します。</span><span class="sxs-lookup"><span data-stu-id="b39e4-168">If LocalDB is not in the list of servers, click the right mouse button on **SQL Server** and select **Add SQL Server** Use the default **Windows Authentication** to connect to the the LocalDB instance</span></span>
3.  <span data-ttu-id="b39e4-169">LocalDB ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="b39e4-169">Expand the LocalDB node</span></span>
4.  <span data-ttu-id="b39e4-170">[ **データベース** ] フォルダーを展開して新しいデータベースを表示し、[ **大学** ] テーブルを参照します。</span><span class="sxs-lookup"><span data-stu-id="b39e4-170">Unfold the **Databases** folder to see the new database and browse to the **Universities** table</span></span>
5.  <span data-ttu-id="b39e4-171">データを表示するには、テーブルを右クリックし、[**データの表示**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="b39e4-171">To view data, right-click on the table and select **View Data**</span></span>

## <a name="summary"></a><span data-ttu-id="b39e4-172">まとめ</span><span class="sxs-lookup"><span data-stu-id="b39e4-172">Summary</span></span>

<span data-ttu-id="b39e4-173">このチュートリアルでは、Entity Framework Code First で空間型を使用する方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="b39e4-173">In this walkthrough we looked at how to use spatial types with Entity Framework Code First.</span></span> 
