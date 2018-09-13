---
title: 空間 - EF6 で Code First
author: divega
ms.date: 10/23/2016
ms.assetid: d617aed1-15f2-48a9-b187-186991c666e3
ms.openlocfilehash: 744ea58c7a8796ed20adc304b3928551eb6928a4
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490598"
---
# <a name="spatial---code-first"></a><span data-ttu-id="d0aed-102">空間の最初のコード</span><span class="sxs-lookup"><span data-stu-id="d0aed-102">Spatial - Code First</span></span>
> [!NOTE]
> <span data-ttu-id="d0aed-103">**EF5 以降のみ**-機能、Api、Entity Framework 5 で導入されたなどのこのページで説明します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-103">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="d0aed-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="d0aed-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="d0aed-105">ビデオ、およびステップ バイ ステップ チュートリアルでは、Entity Framework Code First での空間型をマップする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-105">The video and step-by-step walkthrough shows how to map spatial types with Entity Framework Code First.</span></span> <span data-ttu-id="d0aed-106">LINQ クエリを使用して、2 つの場所間の距離を検索する方法も示します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-106">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="d0aed-107">このチュートリアルは、新しいデータベースを作成する Code First では使用が、使用することも[既存のデータベースを Code First](~/ef6/modeling/code-first/workflows/existing-database.md)します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-107">This walkthrough will use Code First to create a new database, but you can also use [Code First to an existing database](~/ef6/modeling/code-first/workflows/existing-database.md).</span></span>

<span data-ttu-id="d0aed-108">空間型のサポートは、Entity Framework 5 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="d0aed-108">Spatial type support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="d0aed-109">空間型、列挙型、およびテーブル値関数などの新機能を使用する .NET Framework 4.5 をターゲットする必要がありますに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d0aed-109">Note that to use the new features like spatial type, enums, and Table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="d0aed-110">Visual Studio 2012 では、既定では .NET 4.5 を対象とします。</span><span class="sxs-lookup"><span data-stu-id="d0aed-110">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="d0aed-111">空間データ型を使用するには、空間をサポートする Entity Framework プロバイダーを使用することも必要があります。</span><span class="sxs-lookup"><span data-stu-id="d0aed-111">To use spatial data types you must also use an Entity Framework provider that has spatial support.</span></span> <span data-ttu-id="d0aed-112">参照してください[空間型のプロバイダー サポート](~/ef6/fundamentals/providers/spatial-support.md)詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="d0aed-112">See [provider support for spatial types](~/ef6/fundamentals/providers/spatial-support.md) for more information.</span></span>

<span data-ttu-id="d0aed-113">2 つのメインの空間データの種類があります: geography および geometry します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-113">There are two main spatial data types: geography and geometry.</span></span> <span data-ttu-id="d0aed-114">Geography データ型の楕円体データを格納する (たとえば、GPS の緯度と経度座標)。</span><span class="sxs-lookup"><span data-stu-id="d0aed-114">The geography data type stores ellipsoidal data (for example, GPS latitude and longitude coordinates).</span></span> <span data-ttu-id="d0aed-115">Geometry データ型は、ユークリッド (平面) 座標系を表します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-115">The geometry data type represents Euclidean (flat) coordinate system.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="d0aed-116">ビデオを見る</span><span class="sxs-lookup"><span data-stu-id="d0aed-116">Watch the video</span></span>
<span data-ttu-id="d0aed-117">このビデオでは、Entity Framework Code First での空間型をマップする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-117">This video shows how to map spatial types with Entity Framework Code First.</span></span> <span data-ttu-id="d0aed-118">LINQ クエリを使用して、2 つの場所間の距離を検索する方法も示します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-118">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="d0aed-119">**によって提示される**: Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="d0aed-119">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="d0aed-120">**ビデオ**: [WMV](http://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.wmv) | [MP4](http://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-mp4video-spatialwithcodefirst.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.zip)</span><span class="sxs-lookup"><span data-stu-id="d0aed-120">**Video**: [WMV](http://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.wmv) | [MP4](http://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-mp4video-spatialwithcodefirst.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="d0aed-121">前提条件</span><span class="sxs-lookup"><span data-stu-id="d0aed-121">Pre-Requisites</span></span>

<span data-ttu-id="d0aed-122">このチュートリアルを完了にインストールされている Visual Studio 2012、Ultimate、Premium、Professional、または Web Express edition を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d0aed-122">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="d0aed-123">プロジェクトを設定します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-123">Set up the Project</span></span>

1.  <span data-ttu-id="d0aed-124">Visual Studio 2012 を開く</span><span class="sxs-lookup"><span data-stu-id="d0aed-124">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="d0aed-125">**ファイル**メニューで、**新規**、 をクリックし、**プロジェクト**</span><span class="sxs-lookup"><span data-stu-id="d0aed-125">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="d0aed-126">左側のウィンドウで次のようにクリックします**Visual C\#** を選び、**コンソール**テンプレート。</span><span class="sxs-lookup"><span data-stu-id="d0aed-126">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="d0aed-127">入力**SpatialCodeFirst**をクリックして、プロジェクトの名前として**OK**</span><span class="sxs-lookup"><span data-stu-id="d0aed-127">Enter **SpatialCodeFirst** as the name of the project and click **OK**</span></span>

## <a name="define-a-new-model-using-code-first"></a><span data-ttu-id="d0aed-128">Code First を使用して新しいモデルを定義します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-128">Define a New Model using Code First</span></span>

<span data-ttu-id="d0aed-129">Code First の開発を使用する場合に、(ドメイン) の概念モデルを定義する .NET Framework クラスを記述することで、通常で開始します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-129">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span> <span data-ttu-id="d0aed-130">次のコードは、大学のクラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-130">The code below defines the University class.</span></span>

<span data-ttu-id="d0aed-131">大学では、DbGeography 型の場所のプロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="d0aed-131">The University has the Location property of the DbGeography type.</span></span> <span data-ttu-id="d0aed-132">DbGeography の型を使用するには、System.Data.Entity アセンブリへの参照を追加しもステートメントを使用して System.Data.Spatial を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d0aed-132">To use the DbGeography type, you must add a reference to the System.Data.Entity assembly and also add the System.Data.Spatial using statement.</span></span>

<span data-ttu-id="d0aed-133">Program.cs ファイルを開き、次を貼り付け、ファイルの上部にあるステートメントを使用します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-133">Open the Program.cs file and paste the following using statements at the top of the file:</span></span>

``` csharp
using System.Data.Spatial;
```

<span data-ttu-id="d0aed-134">Program.cs ファイルに次の大学クラス定義を追加します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-134">Add the following University class definition to the Program.cs file.</span></span>

``` csharp
public class University  
{
    public int UniversityID { get; set; }
    public string Name { get; set; }
    public DbGeography Location { get; set; }
}
```

## <a name="define-the-dbcontext-derived-type"></a><span data-ttu-id="d0aed-135">定義、DbContext 派生型</span><span class="sxs-lookup"><span data-stu-id="d0aed-135">Define the DbContext Derived Type</span></span>

<span data-ttu-id="d0aed-136">エンティティを定義するだけでなくを DbContext から派生し、DbSet を公開するクラスを定義する必要があります。&lt;TEntity&gt;プロパティ。</span><span class="sxs-lookup"><span data-stu-id="d0aed-136">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="d0aed-137">DbSet&lt;TEntity&gt;プロパティは、モデルに追加する型も把握してコンテキストを使用します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-137">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="d0aed-138">DbContext 派生型のインスタンスがデータベースからデータを使用してオブジェクトの設定が含まれています。 ランタイム処理中に、エンティティ オブジェクトを管理、追跡、および永続化するデータをデータベースに変更します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-138">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="d0aed-139">DbContext、DbSet の型は、EntityFramework アセンブリで定義されます。</span><span class="sxs-lookup"><span data-stu-id="d0aed-139">The DbContext and DbSet types are defined in the EntityFramework assembly.</span></span> <span data-ttu-id="d0aed-140">EntityFramework NuGet パッケージを使用してこの DLL への参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-140">We will add a reference to this DLL by using the EntityFramework NuGet package.</span></span>

1.  <span data-ttu-id="d0aed-141">ソリューション エクスプ ローラーでプロジェクト名を右クリックします。</span><span class="sxs-lookup"><span data-stu-id="d0aed-141">In Solution Explorer, right-click on the project name.</span></span>
2.  <span data-ttu-id="d0aed-142">選択**NuGet パッケージを管理しています.**</span><span class="sxs-lookup"><span data-stu-id="d0aed-142">Select **Manage NuGet Packages…**</span></span>
3.  <span data-ttu-id="d0aed-143">NuGet パッケージの管理 ダイアログ ボックスで、、**オンライン** タブで選択し、 **EntityFramework**パッケージ。</span><span class="sxs-lookup"><span data-stu-id="d0aed-143">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package.</span></span>
4.  <span data-ttu-id="d0aed-144">クリックして**インストール**</span><span class="sxs-lookup"><span data-stu-id="d0aed-144">Click **Install**</span></span>

<span data-ttu-id="d0aed-145">EntityFramework アセンブリだけでなく System.ComponentModel.DataAnnotations アセンブリへの参照も追加されたことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d0aed-145">Note, that in addition to the EntityFramework  assembly, a reference to the System.ComponentModel.DataAnnotations assembly is also added.</span></span>

<span data-ttu-id="d0aed-146">次のコードを追加、Program.cs ファイルの上部にあるステートメントを使用します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-146">At the top of the Program.cs file, add the following using statement:</span></span>

``` csharp
using System.Data.Entity;
```

<span data-ttu-id="d0aed-147">Program.cs では、コンテキストの定義を追加します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-147">In the Program.cs add the context definition.</span></span> 

``` csharp
public partial class UniversityContext : DbContext
{
    public DbSet<University> Universities { get; set; }
}
```

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="d0aed-148">永続化およびデータの取得</span><span class="sxs-lookup"><span data-stu-id="d0aed-148">Persist and Retrieve Data</span></span>

<span data-ttu-id="d0aed-149">Main メソッドが定義されている Program.cs ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="d0aed-149">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="d0aed-150">Main 関数には、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-150">Add the following code into the Main function.</span></span>

<span data-ttu-id="d0aed-151">コードでは、コンテキストに新しい大学の 2 つのオブジェクトを追加します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-151">The code adds two new University objects to the context.</span></span> <span data-ttu-id="d0aed-152">空間プロパティは、DbGeography.FromText メソッドを使用して初期化されます。</span><span class="sxs-lookup"><span data-stu-id="d0aed-152">Spatial properties are initialized by using the DbGeography.FromText method.</span></span> <span data-ttu-id="d0aed-153">地理的ポイント WellKnownText がメソッドに渡されるとして表されます。</span><span class="sxs-lookup"><span data-stu-id="d0aed-153">The geography point represented as WellKnownText is passed to the method.</span></span> <span data-ttu-id="d0aed-154">コードは、データを保存します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-154">The code then saves the data.</span></span> <span data-ttu-id="d0aed-155">次に、LINQ クエリでその場所が指定した場所に最も近い大学オブジェクトを返す構成および実行します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-155">Then, the LINQ query that that returns a University object where its location is closest to the specified location, is constructed and executed.</span></span>

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

<span data-ttu-id="d0aed-156">アプリケーションをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-156">Compile and run the application.</span></span> <span data-ttu-id="d0aed-157">このプログラムの出力は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="d0aed-157">The program produces the following output:</span></span>

```
The closest University to you is: School of Fine Art.
```

## <a name="view-the-generated-database"></a><span data-ttu-id="d0aed-158">生成されたデータベースを表示します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-158">View the Generated Database</span></span>

<span data-ttu-id="d0aed-159">初めてアプリケーションを実行すると、データベースは、Entity Framework によって作成されます。</span><span class="sxs-lookup"><span data-stu-id="d0aed-159">When you run the application the first time, the Entity Framework creates a database for you.</span></span> <span data-ttu-id="d0aed-160">Visual Studio 2012 をインストールしたため、データベースが LocalDB インスタンスで作成されます。</span><span class="sxs-lookup"><span data-stu-id="d0aed-160">Because we have Visual Studio 2012 installed, the database will be created on the LocalDB instance.</span></span> <span data-ttu-id="d0aed-161">既定では、Entity Framework は、派生コンテキストの完全修飾名の後にデータベースを名前 (この例では**SpatialCodeFirst.UniversityContext**)。</span><span class="sxs-lookup"><span data-stu-id="d0aed-161">By default, the Entity Framework names the database after the fully qualified name of the derived context (in this example that is **SpatialCodeFirst.UniversityContext**).</span></span> <span data-ttu-id="d0aed-162">既存のデータベースが使用される後続の時間。</span><span class="sxs-lookup"><span data-stu-id="d0aed-162">The subsequent times the existing database will be used.</span></span>  

<span data-ttu-id="d0aed-163">データベースが作成された後に、モデルに変更を加えた場合 Code First Migrations を使用データベース スキーマを更新する必要がありますに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d0aed-163">Note, that if you make any changes to your model after the database has been created, you should use Code First Migrations to update the database schema.</span></span> <span data-ttu-id="d0aed-164">参照してください[新しいデータベースを Code First](~/ef6/modeling/code-first/workflows/new-database.md) Migrations を使用しての例についてはします。</span><span class="sxs-lookup"><span data-stu-id="d0aed-164">See [Code First to a New Database](~/ef6/modeling/code-first/workflows/new-database.md) for an example of using Migrations.</span></span>

<span data-ttu-id="d0aed-165">データベースとデータを表示するには、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="d0aed-165">To view the database and data, do the following:</span></span>

1.  <span data-ttu-id="d0aed-166">Visual Studio 2012 のメイン メニューで、次のように選択します。**ビュー**  - &gt; **SQL Server オブジェクト エクスプ ローラー**します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-166">In the Visual Studio 2012 main menu, select **View** -&gt; **SQL Server Object Explorer**.</span></span>
2.  <span data-ttu-id="d0aed-167">サーバーの一覧で、LocalDB でない場合にマウスの右ボタンをクリックします**SQL Server**選択と**SQL Server の追加**既定値を使用して、 **Windows 認証**への接続に、。LocalDB インスタンス</span><span class="sxs-lookup"><span data-stu-id="d0aed-167">If LocalDB is not in the list of servers, click the right mouse button on **SQL Server** and select **Add SQL Server** Use the default **Windows Authentication** to connect to the the LocalDB instance</span></span>
3.  <span data-ttu-id="d0aed-168">LocalDB のノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="d0aed-168">Expand the LocalDB node</span></span>
4.  <span data-ttu-id="d0aed-169">Unfold、**データベース**フォルダーを新しいデータベースを表示しを参照、**大学**テーブル</span><span class="sxs-lookup"><span data-stu-id="d0aed-169">Unfold the **Databases** folder to see the new database and browse to the **Universities** table</span></span>
5.  <span data-ttu-id="d0aed-170">データを表示し、テーブルを右クリックして、選択**データの表示**</span><span class="sxs-lookup"><span data-stu-id="d0aed-170">To view data, right-click on the table and select **View Data**</span></span>

## <a name="summary"></a><span data-ttu-id="d0aed-171">まとめ</span><span class="sxs-lookup"><span data-stu-id="d0aed-171">Summary</span></span>

<span data-ttu-id="d0aed-172">このチュートリアルでは、Entity Framework Code First で空間型を使用する方法を説明しました。</span><span class="sxs-lookup"><span data-stu-id="d0aed-172">In this walkthrough we looked at how to use spatial types with Entity Framework Code First.</span></span> 
