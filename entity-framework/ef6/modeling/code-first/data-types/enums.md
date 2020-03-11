---
title: Enum サポート-Code First-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 77a42501-27c9-4f4b-96df-26c128021467
ms.openlocfilehash: 1cecbf7065367deb3d202977fe39187bd907d824
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415791"
---
# <a name="enum-support---code-first"></a><span data-ttu-id="66d87-102">列挙型のサポート-Code First</span><span class="sxs-lookup"><span data-stu-id="66d87-102">Enum Support - Code First</span></span>
> [!NOTE]
> <span data-ttu-id="66d87-103">**EF5**以降: このページで説明した機能、api などは、Entity Framework 5 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="66d87-103">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="66d87-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="66d87-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="66d87-105">このビデオとステップバイステップのチュートリアルでは、Entity Framework Code First で列挙型を使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="66d87-105">This video and step-by-step walkthrough shows how to use enum types with Entity Framework Code First.</span></span> <span data-ttu-id="66d87-106">また、LINQ クエリで列挙型を使用する方法も示します。</span><span class="sxs-lookup"><span data-stu-id="66d87-106">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="66d87-107">このチュートリアルでは Code First を使用して新しいデータベースを作成しますが、Code First を使用して[既存のデータベースにマップ](~/ef6/modeling/code-first/workflows/existing-database.md)することもできます。</span><span class="sxs-lookup"><span data-stu-id="66d87-107">This walkthrough will use Code First to create a new database, but you can also use [Code First to map to an existing database](~/ef6/modeling/code-first/workflows/existing-database.md).</span></span>

<span data-ttu-id="66d87-108">Enum サポートは Entity Framework 5 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="66d87-108">Enum support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="66d87-109">列挙型、空間データ型、テーブル値関数などの新機能を使用するには、.NET Framework 4.5 を対象にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="66d87-109">To use the new features like enums, spatial data types, and table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="66d87-110">既定では、Visual Studio 2012 は .NET 4.5 を対象としています。</span><span class="sxs-lookup"><span data-stu-id="66d87-110">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="66d87-111">Entity Framework では、列挙体には、 **Byte**、 **Int16**、 **Int32**、 **Int64** 、または**SByte**の各基になる型を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="66d87-111">In Entity Framework, an enumeration can have the following underlying types: **Byte**, **Int16**, **Int32**, **Int64** , or **SByte**.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="66d87-112">ビデオを見る</span><span class="sxs-lookup"><span data-stu-id="66d87-112">Watch the video</span></span>
<span data-ttu-id="66d87-113">このビデオでは、Entity Framework Code First で列挙型を使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="66d87-113">This video shows how to use enum types with Entity Framework Code First.</span></span> <span data-ttu-id="66d87-114">また、LINQ クエリで列挙型を使用する方法も示します。</span><span class="sxs-lookup"><span data-stu-id="66d87-114">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="66d87-115">**表示者**: ジュリアつい</span><span class="sxs-lookup"><span data-stu-id="66d87-115">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="66d87-116">**ビデオ**: [wmv](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.wmv) | [MP4](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-mp4video-enumwithcodefirst.m4v) | [wmv (ZIP)](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.zip)</span><span class="sxs-lookup"><span data-stu-id="66d87-116">**Video**: [WMV](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.wmv) | [MP4](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-mp4video-enumwithcodefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="66d87-117">前提条件</span><span class="sxs-lookup"><span data-stu-id="66d87-117">Pre-Requisites</span></span>

<span data-ttu-id="66d87-118">このチュートリアルを完了するには、Visual Studio 2012、Ultimate、Premium、Professional、または Web Express エディションがインストールされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="66d87-118">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

 

## <a name="set-up-the-project"></a><span data-ttu-id="66d87-119">プロジェクトを設定する</span><span class="sxs-lookup"><span data-stu-id="66d87-119">Set up the Project</span></span>

1.  <span data-ttu-id="66d87-120">Visual Studio 2012 を開く</span><span class="sxs-lookup"><span data-stu-id="66d87-120">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="66d87-121">**[ファイル]** メニューの **[新規作成]** をポイントし、 **[プロジェクト]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="66d87-121">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="66d87-122">左側のウィンドウで、[ **Visual C\#** ] をクリックし、**コンソール**テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="66d87-122">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="66d87-123">プロジェクトの名前として「 **Enumcodefirst** 」と入力し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="66d87-123">Enter **EnumCodeFirst** as the name of the project and click **OK**</span></span>

## <a name="define-a-new-model-using-code-first"></a><span data-ttu-id="66d87-124">Code First を使用して新しいモデルを定義する</span><span class="sxs-lookup"><span data-stu-id="66d87-124">Define a New Model using Code First</span></span>

<span data-ttu-id="66d87-125">Code First 開発を使用する場合は、通常、概念 (ドメイン) モデルを定義する .NET Framework クラスを記述することから始めます。</span><span class="sxs-lookup"><span data-stu-id="66d87-125">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span> <span data-ttu-id="66d87-126">次のコードでは、Department クラスを定義しています。</span><span class="sxs-lookup"><span data-stu-id="66d87-126">The code below defines the Department class.</span></span>

<span data-ttu-id="66d87-127">このコードでは、DepartmentNames 列挙型も定義されています。</span><span class="sxs-lookup"><span data-stu-id="66d87-127">The code also defines the DepartmentNames enumeration.</span></span> <span data-ttu-id="66d87-128">既定では、列挙は**int**型です。</span><span class="sxs-lookup"><span data-stu-id="66d87-128">By default, the enumeration is of **int** type.</span></span> <span data-ttu-id="66d87-129">Department クラスの Name プロパティは、DepartmentNames 型です。</span><span class="sxs-lookup"><span data-stu-id="66d87-129">The Name property on the Department class is of the DepartmentNames type.</span></span>

<span data-ttu-id="66d87-130">Program.cs ファイルを開き、次のクラス定義を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="66d87-130">Open the Program.cs file and paste the following class definitions.</span></span>

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
 

## <a name="define-the-dbcontext-derived-type"></a><span data-ttu-id="66d87-131">DbContext の派生型を定義する</span><span class="sxs-lookup"><span data-stu-id="66d87-131">Define the DbContext Derived Type</span></span>

<span data-ttu-id="66d87-132">エンティティを定義するだけでなく、DbContext から派生するクラスを定義し、Dbcontext&lt;TEntity&gt; プロパティを公開する必要があります。</span><span class="sxs-lookup"><span data-stu-id="66d87-132">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="66d87-133">DbSet&lt;TEntity&gt; プロパティを使用すると、モデルに含める型をコンテキストに認識させることができます。</span><span class="sxs-lookup"><span data-stu-id="66d87-133">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="66d87-134">DbContext の派生型のインスタンスは、実行時にエンティティオブジェクトを管理します。これには、データベースからのデータを使用したオブジェクトの読み込み、変更の追跡、およびデータベースへのデータの永続化が含まれます。</span><span class="sxs-lookup"><span data-stu-id="66d87-134">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="66d87-135">DbContext および Dbcontext 型は EntityFramework アセンブリで定義されています。</span><span class="sxs-lookup"><span data-stu-id="66d87-135">The DbContext and DbSet types are defined in the EntityFramework assembly.</span></span> <span data-ttu-id="66d87-136">ここでは、EntityFramework NuGet パッケージを使用して、この DLL への参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="66d87-136">We will add a reference to this DLL by using the EntityFramework NuGet package.</span></span>

1.  <span data-ttu-id="66d87-137">ソリューションエクスプローラーで、プロジェクト名を右クリックします。</span><span class="sxs-lookup"><span data-stu-id="66d87-137">In Solution Explorer, right-click on the project name.</span></span>
2.  <span data-ttu-id="66d87-138">**[NuGet パッケージの管理...]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="66d87-138">Select **Manage NuGet Packages…**</span></span>
3.  <span data-ttu-id="66d87-139">NuGet パッケージの管理 ダイアログで、**オンライン** タブを選択し、 **entityframework**パッケージを選択します。</span><span class="sxs-lookup"><span data-stu-id="66d87-139">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package.</span></span>
4.  <span data-ttu-id="66d87-140">**[インストール]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="66d87-140">Click **Install**</span></span>

<span data-ttu-id="66d87-141">EntityFramework アセンブリに加えて、System.componentmodel アセンブリおよび system.object アセンブリへの参照も追加されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="66d87-141">Note, that in addition to the EntityFramework  assembly, references to System.ComponentModel.DataAnnotations and System.Data.Entity assemblies are added as well.</span></span>

<span data-ttu-id="66d87-142">Program.cs ファイルの先頭に、次の using ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="66d87-142">At the top of the Program.cs file, add the following using statement:</span></span>

``` csharp
using System.Data.Entity;
```

<span data-ttu-id="66d87-143">Program.cs で、コンテキスト定義を追加します。</span><span class="sxs-lookup"><span data-stu-id="66d87-143">In the Program.cs add the context definition.</span></span> 

``` csharp
public partial class EnumTestContext : DbContext
{
    public DbSet<Department> Departments { get; set; }
}
```
 

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="66d87-144">データの永続化と取得</span><span class="sxs-lookup"><span data-stu-id="66d87-144">Persist and Retrieve Data</span></span>

<span data-ttu-id="66d87-145">Main メソッドが定義されている Program.cs ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="66d87-145">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="66d87-146">Main 関数に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="66d87-146">Add the following code into the Main function.</span></span> <span data-ttu-id="66d87-147">このコードは、新しい Department オブジェクトをコンテキストに追加します。</span><span class="sxs-lookup"><span data-stu-id="66d87-147">The code adds a new Department object to the context.</span></span> <span data-ttu-id="66d87-148">その後、データを保存します。</span><span class="sxs-lookup"><span data-stu-id="66d87-148">It then saves the data.</span></span> <span data-ttu-id="66d87-149">また、このコードでは、名前が DepartmentNames である Department を返す LINQ クエリも実行されます。</span><span class="sxs-lookup"><span data-stu-id="66d87-149">The code also executes a LINQ query that returns a Department where the name is DepartmentNames.English.</span></span>

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

<span data-ttu-id="66d87-150">アプリケーションをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="66d87-150">Compile and run the application.</span></span> <span data-ttu-id="66d87-151">このプログラムの出力は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="66d87-151">The program produces the following output:</span></span>

``` csharp
DepartmentID: 1 Name: English
```
 

## <a name="view-the-generated-database"></a><span data-ttu-id="66d87-152">生成されたデータベースを表示する</span><span class="sxs-lookup"><span data-stu-id="66d87-152">View the Generated Database</span></span>

<span data-ttu-id="66d87-153">アプリケーションを初めて実行すると、Entity Framework によってデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="66d87-153">When you run the application the first time, the Entity Framework creates a database for you.</span></span> <span data-ttu-id="66d87-154">Visual Studio 2012 がインストールされているため、データベースは LocalDB インスタンス上に作成されます。</span><span class="sxs-lookup"><span data-stu-id="66d87-154">Because we have Visual Studio 2012 installed, the database will be created on the LocalDB instance.</span></span> <span data-ttu-id="66d87-155">既定では、Entity Framework は、派生コンテキストの完全修飾名の後にデータベースに名前を付けます (この例では、 **Enumcodefirst**)。</span><span class="sxs-lookup"><span data-stu-id="66d87-155">By default, the Entity Framework names the database after the fully qualified name of the derived context (for this example that is **EnumCodeFirst.EnumTestContext**).</span></span> <span data-ttu-id="66d87-156">その後、既存のデータベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="66d87-156">The subsequent times the existing database will be used.</span></span>  

<span data-ttu-id="66d87-157">データベースの作成後にモデルに変更を加えた場合は、Code First Migrations を使用してデータベーススキーマを更新する必要があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="66d87-157">Note, that if you make any changes to your model after the database has been created, you should use Code First Migrations to update the database schema.</span></span> <span data-ttu-id="66d87-158">移行の使用例については[、「新しいデータベースへの Code First](~/ef6/modeling/code-first/workflows/new-database.md) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="66d87-158">See [Code First to a New Database](~/ef6/modeling/code-first/workflows/new-database.md) for an example of using Migrations.</span></span>

<span data-ttu-id="66d87-159">データベースとデータを表示するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="66d87-159">To view the database and data, do the following:</span></span>

1.  <span data-ttu-id="66d87-160">Visual Studio 2012 のメインメニューで、[ **View** -&gt; **SQL Server オブジェクトエクスプローラー**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="66d87-160">In the Visual Studio 2012 main menu, select **View** -&gt; **SQL Server Object Explorer**.</span></span>
2.  <span data-ttu-id="66d87-161">LocalDB がサーバーの一覧にない場合は、 **SQL Server**で右マウスボタンをクリックして [追加] を選択**SQL Server** 、既定の**Windows 認証**を使用して localdb インスタンスに接続します。</span><span class="sxs-lookup"><span data-stu-id="66d87-161">If LocalDB is not in the list of servers, click the right mouse button on **SQL Server** and select **Add SQL Server** Use the default **Windows Authentication** to connect to the LocalDB instance</span></span>
3.  <span data-ttu-id="66d87-162">LocalDB ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="66d87-162">Expand the LocalDB node</span></span>
4.  <span data-ttu-id="66d87-163">**[データベース]** フォルダーを展開して新しいデータベースを表示し、 **Department**テーブルに参照します。ただし、列挙型にマップするテーブルは作成されません Code First</span><span class="sxs-lookup"><span data-stu-id="66d87-163">Unfold the **Databases** folder to see the new database and browse to the **Department** table Note, that Code First does not create a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="66d87-164">データを表示するには、テーブルを右クリックし、 **[データの表示]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="66d87-164">To view data, right-click on the table and select **View Data**</span></span>

## <a name="summary"></a><span data-ttu-id="66d87-165">まとめ</span><span class="sxs-lookup"><span data-stu-id="66d87-165">Summary</span></span>

<span data-ttu-id="66d87-166">このチュートリアルでは、Entity Framework Code First で列挙型を使用する方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="66d87-166">In this walkthrough we looked at how to use enum types with Entity Framework Code First.</span></span> 
