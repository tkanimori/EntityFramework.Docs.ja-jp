---
title: 列挙型のサポートには、EF6 で Code First
author: divega
ms.date: 10/23/2016
ms.assetid: 77a42501-27c9-4f4b-96df-26c128021467
ms.openlocfilehash: 1cecbf7065367deb3d202977fe39187bd907d824
ms.sourcegitcommit: 269c8a1a457a9ad27b4026c22c4b1a76991fb360
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46283721"
---
# <a name="enum-support---code-first"></a><span data-ttu-id="3ac25-102">列挙型のサポート - Code First</span><span class="sxs-lookup"><span data-stu-id="3ac25-102">Enum Support - Code First</span></span>
> [!NOTE]
> <span data-ttu-id="3ac25-103">**EF5 以降のみ**-機能、Api、Entity Framework 5 で導入されたなどのこのページで説明します。</span><span class="sxs-lookup"><span data-stu-id="3ac25-103">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="3ac25-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="3ac25-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="3ac25-105">このビデオ、およびステップ バイ ステップ チュートリアルでは、Entity Framework Code First で列挙型を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="3ac25-105">This video and step-by-step walkthrough shows how to use enum types with Entity Framework Code First.</span></span> <span data-ttu-id="3ac25-106">LINQ クエリで列挙型を使用する方法も示します。</span><span class="sxs-lookup"><span data-stu-id="3ac25-106">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="3ac25-107">このチュートリアルは、新しいデータベースを作成する Code First では使用が、使用することも[既存のデータベースにマップする Code First](~/ef6/modeling/code-first/workflows/existing-database.md)します。</span><span class="sxs-lookup"><span data-stu-id="3ac25-107">This walkthrough will use Code First to create a new database, but you can also use [Code First to map to an existing database](~/ef6/modeling/code-first/workflows/existing-database.md).</span></span>

<span data-ttu-id="3ac25-108">列挙型のサポートは、Entity Framework 5 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="3ac25-108">Enum support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="3ac25-109">列挙型、空間データ型、およびテーブル値関数などの新機能を使用するには、.NET Framework 4.5 を対象する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3ac25-109">To use the new features like enums, spatial data types, and table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="3ac25-110">Visual Studio 2012 では、既定では .NET 4.5 を対象とします。</span><span class="sxs-lookup"><span data-stu-id="3ac25-110">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="3ac25-111">列挙体では、Entity Framework では、次の基になる型を持つことができます:**バイト**、 **Int16**、 **Int32**、 **Int64** 、または**SByte**します。</span><span class="sxs-lookup"><span data-stu-id="3ac25-111">In Entity Framework, an enumeration can have the following underlying types: **Byte**, **Int16**, **Int32**, **Int64** , or **SByte**.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="3ac25-112">ビデオを見る</span><span class="sxs-lookup"><span data-stu-id="3ac25-112">Watch the video</span></span>
<span data-ttu-id="3ac25-113">このビデオでは、Entity Framework Code First で列挙型を使用する方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="3ac25-113">This video shows how to use enum types with Entity Framework Code First.</span></span> <span data-ttu-id="3ac25-114">LINQ クエリで列挙型を使用する方法も示します。</span><span class="sxs-lookup"><span data-stu-id="3ac25-114">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="3ac25-115">**によって提示される**: Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="3ac25-115">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="3ac25-116">**ビデオ**: [WMV](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.wmv) | [MP4](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-mp4video-enumwithcodefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.zip)</span><span class="sxs-lookup"><span data-stu-id="3ac25-116">**Video**: [WMV](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.wmv) | [MP4](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-mp4video-enumwithcodefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="3ac25-117">前提条件</span><span class="sxs-lookup"><span data-stu-id="3ac25-117">Pre-Requisites</span></span>

<span data-ttu-id="3ac25-118">このチュートリアルを完了にインストールされている Visual Studio 2012、Ultimate、Premium、Professional、または Web Express edition を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3ac25-118">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

 

## <a name="set-up-the-project"></a><span data-ttu-id="3ac25-119">プロジェクトを設定します。</span><span class="sxs-lookup"><span data-stu-id="3ac25-119">Set up the Project</span></span>

1.  <span data-ttu-id="3ac25-120">Visual Studio 2012 を開く</span><span class="sxs-lookup"><span data-stu-id="3ac25-120">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="3ac25-121">**ファイル**メニューで、**新規**、 をクリックし、**プロジェクト**</span><span class="sxs-lookup"><span data-stu-id="3ac25-121">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="3ac25-122">左側のウィンドウで次のようにクリックします**Visual C\#** を選び、**コンソール**テンプレート。</span><span class="sxs-lookup"><span data-stu-id="3ac25-122">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="3ac25-123">入力**EnumCodeFirst**をクリックして、プロジェクトの名前として**OK**</span><span class="sxs-lookup"><span data-stu-id="3ac25-123">Enter **EnumCodeFirst** as the name of the project and click **OK**</span></span>

## <a name="define-a-new-model-using-code-first"></a><span data-ttu-id="3ac25-124">Code First を使用して新しいモデルを定義します。</span><span class="sxs-lookup"><span data-stu-id="3ac25-124">Define a New Model using Code First</span></span>

<span data-ttu-id="3ac25-125">Code First の開発を使用する場合に、(ドメイン) の概念モデルを定義する .NET Framework クラスを記述することで、通常で開始します。</span><span class="sxs-lookup"><span data-stu-id="3ac25-125">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span> <span data-ttu-id="3ac25-126">次のコードは、部門のクラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="3ac25-126">The code below defines the Department class.</span></span>

<span data-ttu-id="3ac25-127">コードでは、DepartmentNames 列挙型も定義します。</span><span class="sxs-lookup"><span data-stu-id="3ac25-127">The code also defines the DepartmentNames enumeration.</span></span> <span data-ttu-id="3ac25-128">既定では、列挙型はの**int**型。</span><span class="sxs-lookup"><span data-stu-id="3ac25-128">By default, the enumeration is of **int** type.</span></span> <span data-ttu-id="3ac25-129">部門クラスの名前プロパティは、DepartmentNames 型のです。</span><span class="sxs-lookup"><span data-stu-id="3ac25-129">The Name property on the Department class is of the DepartmentNames type.</span></span>

<span data-ttu-id="3ac25-130">Program.cs ファイルを開き、次のクラス定義を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="3ac25-130">Open the Program.cs file and paste the following class definitions.</span></span>

``` csharp
public enum DepartmentNames
{
    English,
    Math,
    Economics
}     

public partial class Department
{
    public int DepartmentID { get; set; }
    public DepartmentNames Name { get; set; }
    public decimal Budget { get; set; }
}
```
 

## <a name="define-the-dbcontext-derived-type"></a><span data-ttu-id="3ac25-131">定義、DbContext 派生型</span><span class="sxs-lookup"><span data-stu-id="3ac25-131">Define the DbContext Derived Type</span></span>

<span data-ttu-id="3ac25-132">エンティティを定義するだけでなくを DbContext から派生し、DbSet を公開するクラスを定義する必要があります。&lt;TEntity&gt;プロパティ。</span><span class="sxs-lookup"><span data-stu-id="3ac25-132">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="3ac25-133">DbSet&lt;TEntity&gt;プロパティは、モデルに追加する型も把握してコンテキストを使用します。</span><span class="sxs-lookup"><span data-stu-id="3ac25-133">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="3ac25-134">DbContext 派生型のインスタンスがデータベースからデータを使用してオブジェクトの設定が含まれています。 ランタイム処理中に、エンティティ オブジェクトを管理、追跡、および永続化するデータをデータベースに変更します。</span><span class="sxs-lookup"><span data-stu-id="3ac25-134">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="3ac25-135">DbContext、DbSet の型は、EntityFramework アセンブリで定義されます。</span><span class="sxs-lookup"><span data-stu-id="3ac25-135">The DbContext and DbSet types are defined in the EntityFramework assembly.</span></span> <span data-ttu-id="3ac25-136">EntityFramework NuGet パッケージを使用してこの DLL への参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="3ac25-136">We will add a reference to this DLL by using the EntityFramework NuGet package.</span></span>

1.  <span data-ttu-id="3ac25-137">ソリューション エクスプ ローラーでプロジェクト名を右クリックします。</span><span class="sxs-lookup"><span data-stu-id="3ac25-137">In Solution Explorer, right-click on the project name.</span></span>
2.  <span data-ttu-id="3ac25-138">選択**NuGet パッケージを管理しています.**</span><span class="sxs-lookup"><span data-stu-id="3ac25-138">Select **Manage NuGet Packages…**</span></span>
3.  <span data-ttu-id="3ac25-139">NuGet パッケージの管理 ダイアログ ボックスで、、**オンライン** タブで選択し、 **EntityFramework**パッケージ。</span><span class="sxs-lookup"><span data-stu-id="3ac25-139">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package.</span></span>
4.  <span data-ttu-id="3ac25-140">クリックして**インストール**</span><span class="sxs-lookup"><span data-stu-id="3ac25-140">Click **Install**</span></span>

<span data-ttu-id="3ac25-141">EntityFramework アセンブリだけでなく System.ComponentModel.DataAnnotations と System.Data.Entity アセンブリへの参照が追加されたことにも注意してください。</span><span class="sxs-lookup"><span data-stu-id="3ac25-141">Note, that in addition to the EntityFramework  assembly, references to System.ComponentModel.DataAnnotations and System.Data.Entity assemblies are added as well.</span></span>

<span data-ttu-id="3ac25-142">次のコードを追加、Program.cs ファイルの上部にあるステートメントを使用します。</span><span class="sxs-lookup"><span data-stu-id="3ac25-142">At the top of the Program.cs file, add the following using statement:</span></span>

``` csharp
using System.Data.Entity;
```

<span data-ttu-id="3ac25-143">Program.cs では、コンテキストの定義を追加します。</span><span class="sxs-lookup"><span data-stu-id="3ac25-143">In the Program.cs add the context definition.</span></span> 

``` csharp
public partial class EnumTestContext : DbContext
{
    public DbSet<Department> Departments { get; set; }
}
```
 

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="3ac25-144">永続化およびデータの取得</span><span class="sxs-lookup"><span data-stu-id="3ac25-144">Persist and Retrieve Data</span></span>

<span data-ttu-id="3ac25-145">Main メソッドが定義されている Program.cs ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="3ac25-145">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="3ac25-146">Main 関数には、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="3ac25-146">Add the following code into the Main function.</span></span> <span data-ttu-id="3ac25-147">コードでは、コンテキストに新しい部門オブジェクトを追加します。</span><span class="sxs-lookup"><span data-stu-id="3ac25-147">The code adds a new Department object to the context.</span></span> <span data-ttu-id="3ac25-148">データが保存されます。</span><span class="sxs-lookup"><span data-stu-id="3ac25-148">It then saves the data.</span></span> <span data-ttu-id="3ac25-149">コードでは、名前は DepartmentNames.English 部門を返す LINQ クエリも実行します。</span><span class="sxs-lookup"><span data-stu-id="3ac25-149">The code also executes a LINQ query that returns a Department where the name is DepartmentNames.English.</span></span>

``` csharp
using (var context = new EnumTestContext())
{
    context.Departments.Add(new Department { Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

<span data-ttu-id="3ac25-150">アプリケーションをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="3ac25-150">Compile and run the application.</span></span> <span data-ttu-id="3ac25-151">このプログラムの出力は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="3ac25-151">The program produces the following output:</span></span>

``` csharp
DepartmentID: 1 Name: English
```
 

## <a name="view-the-generated-database"></a><span data-ttu-id="3ac25-152">生成されたデータベースを表示します。</span><span class="sxs-lookup"><span data-stu-id="3ac25-152">View the Generated Database</span></span>

<span data-ttu-id="3ac25-153">初めてアプリケーションを実行すると、データベースは、Entity Framework によって作成されます。</span><span class="sxs-lookup"><span data-stu-id="3ac25-153">When you run the application the first time, the Entity Framework creates a database for you.</span></span> <span data-ttu-id="3ac25-154">Visual Studio 2012 をインストールしたため、データベースが LocalDB インスタンスで作成されます。</span><span class="sxs-lookup"><span data-stu-id="3ac25-154">Because we have Visual Studio 2012 installed, the database will be created on the LocalDB instance.</span></span> <span data-ttu-id="3ac25-155">既定では、Entity Framework は、派生コンテキストの完全修飾名の後にデータベースを名前 (この例では、 **EnumCodeFirst.EnumTestContext**)。</span><span class="sxs-lookup"><span data-stu-id="3ac25-155">By default, the Entity Framework names the database after the fully qualified name of the derived context (for this example that is **EnumCodeFirst.EnumTestContext**).</span></span> <span data-ttu-id="3ac25-156">既存のデータベースが使用される後続の時間。</span><span class="sxs-lookup"><span data-stu-id="3ac25-156">The subsequent times the existing database will be used.</span></span>  

<span data-ttu-id="3ac25-157">データベースが作成された後に、モデルに変更を加えた場合 Code First Migrations を使用データベース スキーマを更新する必要がありますに注意してください。</span><span class="sxs-lookup"><span data-stu-id="3ac25-157">Note, that if you make any changes to your model after the database has been created, you should use Code First Migrations to update the database schema.</span></span> <span data-ttu-id="3ac25-158">参照してください[新しいデータベースを Code First](~/ef6/modeling/code-first/workflows/new-database.md) Migrations を使用しての例についてはします。</span><span class="sxs-lookup"><span data-stu-id="3ac25-158">See [Code First to a New Database](~/ef6/modeling/code-first/workflows/new-database.md) for an example of using Migrations.</span></span>

<span data-ttu-id="3ac25-159">データベースとデータを表示するには、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="3ac25-159">To view the database and data, do the following:</span></span>

1.  <span data-ttu-id="3ac25-160">Visual Studio 2012 のメイン メニューで、次のように選択します。**ビュー**  - &gt; **SQL Server オブジェクト エクスプ ローラー**します。</span><span class="sxs-lookup"><span data-stu-id="3ac25-160">In the Visual Studio 2012 main menu, select **View** -&gt; **SQL Server Object Explorer**.</span></span>
2.  <span data-ttu-id="3ac25-161">サーバーの一覧で、LocalDB でない場合にマウスの右ボタンをクリックします**SQL Server**選択と**SQL Server の追加**既定値を使用して、 **Windows 認証**への接続に、。LocalDB インスタンス</span><span class="sxs-lookup"><span data-stu-id="3ac25-161">If LocalDB is not in the list of servers, click the right mouse button on **SQL Server** and select **Add SQL Server** Use the default **Windows Authentication** to connect to the LocalDB instance</span></span>
3.  <span data-ttu-id="3ac25-162">LocalDB のノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="3ac25-162">Expand the LocalDB node</span></span>
4.  <span data-ttu-id="3ac25-163">Unfold、**データベース**フォルダーを新しいデータベースを表示しを参照、**部門**テーブルの Code First はテーブルを作成できません列挙型にマップされるに注意してください</span><span class="sxs-lookup"><span data-stu-id="3ac25-163">Unfold the **Databases** folder to see the new database and browse to the **Department** table Note, that Code First does not create a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="3ac25-164">データを表示し、テーブルを右クリックして、選択**データの表示**</span><span class="sxs-lookup"><span data-stu-id="3ac25-164">To view data, right-click on the table and select **View Data**</span></span>

## <a name="summary"></a><span data-ttu-id="3ac25-165">まとめ</span><span class="sxs-lookup"><span data-stu-id="3ac25-165">Summary</span></span>

<span data-ttu-id="3ac25-166">このチュートリアルでは、Entity Framework Code First と列挙型を使用する方法を説明しました。</span><span class="sxs-lookup"><span data-stu-id="3ac25-166">In this walkthrough we looked at how to use enum types with Entity Framework Code First.</span></span> 
