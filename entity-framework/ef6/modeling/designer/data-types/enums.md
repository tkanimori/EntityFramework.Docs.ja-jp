---
title: 列挙型のサポート - EF デザイナー - EF6
author: divega
ms.date: 2016-10-23
ms.assetid: c6ae6d8f-1ace-47db-ad47-b1718f1ba082
ms.openlocfilehash: d4c5528c4dc13ab7189421feebf84c2cb2f4b2bb
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995638"
---
# <a name="enum-support---ef-designer"></a><span data-ttu-id="29314-102">列挙型のサポート - EF Designer</span><span class="sxs-lookup"><span data-stu-id="29314-102">Enum Support - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="29314-103">**EF5 以降のみ**-機能、Api、Entity Framework 5 で導入されたなどのこのページで説明します。</span><span class="sxs-lookup"><span data-stu-id="29314-103">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="29314-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="29314-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="29314-105">このビデオ、およびステップ バイ ステップ チュートリアルでは、Entity Framework デザイナーを使用した列挙型を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="29314-105">This video and step-by-step walkthrough shows how to use enum types with the Entity Framework Designer.</span></span> <span data-ttu-id="29314-106">LINQ クエリで列挙型を使用する方法も示します。</span><span class="sxs-lookup"><span data-stu-id="29314-106">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="29314-107">このチュートリアルで、新しいデータベースを作成する最初のモデル使用しますが、EF デザイナーはでも使用できます、 [Database First](~/ef6/modeling/designer/workflows/database-first.md)ワークフローに既存のデータベースにマップします。</span><span class="sxs-lookup"><span data-stu-id="29314-107">This walkthrough will use Model First to create a new database, but the EF Designer can also be used with the [Database First](~/ef6/modeling/designer/workflows/database-first.md) workflow to map to an existing database.</span></span>

<span data-ttu-id="29314-108">列挙型のサポートは、Entity Framework 5 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="29314-108">Enum support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="29314-109">列挙型、空間データ型、およびテーブル値関数などの新機能を使用するには、.NET Framework 4.5 を対象する必要があります。</span><span class="sxs-lookup"><span data-stu-id="29314-109">To use the new features like enums, spatial data types, and table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="29314-110">Visual Studio 2012 では、既定では .NET 4.5 を対象とします。</span><span class="sxs-lookup"><span data-stu-id="29314-110">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="29314-111">列挙体では、Entity Framework では、次の基になる型を持つことができます:**バイト**、 **Int16**、 **Int32**、 **Int64** 、または**SByte**します。</span><span class="sxs-lookup"><span data-stu-id="29314-111">In Entity Framework, an enumeration can have the following underlying types: **Byte**, **Int16**, **Int32**, **Int64** , or **SByte**.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="29314-112">ビデオを見る</span><span class="sxs-lookup"><span data-stu-id="29314-112">Watch the Video</span></span>
<span data-ttu-id="29314-113">このビデオでは、Entity Framework デザイナーを使用した列挙型を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="29314-113">This video shows how to use enum types with the Entity Framework Designer.</span></span> <span data-ttu-id="29314-114">LINQ クエリで列挙型を使用する方法も示します。</span><span class="sxs-lookup"><span data-stu-id="29314-114">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="29314-115">**によって提示される**: Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="29314-115">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="29314-116">**ビデオ**: [WMV](http://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv) | [MP4](http://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)</span><span class="sxs-lookup"><span data-stu-id="29314-116">**Video**: [WMV](http://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv) | [MP4](http://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="29314-117">前提条件</span><span class="sxs-lookup"><span data-stu-id="29314-117">Pre-Requisites</span></span>

<span data-ttu-id="29314-118">このチュートリアルを完了にインストールされている Visual Studio 2012、Ultimate、Premium、Professional、または Web Express edition を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="29314-118">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="29314-119">プロジェクトを設定します。</span><span class="sxs-lookup"><span data-stu-id="29314-119">Set up the Project</span></span>

1.  <span data-ttu-id="29314-120">Visual Studio 2012 を開く</span><span class="sxs-lookup"><span data-stu-id="29314-120">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="29314-121">**ファイル**メニューで、**新規**、 をクリックし、**プロジェクト**</span><span class="sxs-lookup"><span data-stu-id="29314-121">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="29314-122">左側のウィンドウで次のようにクリックします**Visual C\#** を選び、**コンソール**テンプレート。</span><span class="sxs-lookup"><span data-stu-id="29314-122">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="29314-123">入力**EnumEFDesigner**をクリックして、プロジェクトの名前として**OK**</span><span class="sxs-lookup"><span data-stu-id="29314-123">Enter **EnumEFDesigner** as the name of the project and click **OK**</span></span>

## <a name="create-a-new-model-using-the-ef-designer"></a><span data-ttu-id="29314-124">EF デザイナーを使用して、新しいモデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="29314-124">Create a New Model using the EF Designer</span></span>

1.  <span data-ttu-id="29314-125">ソリューション エクスプ ローラーでプロジェクト名を右クリックし、[**追加**、] をクリックし、**新しい項目**</span><span class="sxs-lookup"><span data-stu-id="29314-125">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="29314-126">選択**データ**選択し、左側のメニューから**ADO.NET Entity Data Model**テンプレート ペイン</span><span class="sxs-lookup"><span data-stu-id="29314-126">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane</span></span>
3.  <span data-ttu-id="29314-127">入力**EnumTestModel.edmx**のファイル名、およびクリック**追加**</span><span class="sxs-lookup"><span data-stu-id="29314-127">Enter **EnumTestModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="29314-128">Entity Data Model ウィザード ページで、次のように選択します**空のモデル**モデルのコンテンツの選択 ダイアログ ボックス。</span><span class="sxs-lookup"><span data-stu-id="29314-128">On the Entity Data Model Wizard page, select **Empty Model** in the Choose Model Contents dialog box</span></span>
5.  <span data-ttu-id="29314-129">クリックして**完了**</span><span class="sxs-lookup"><span data-stu-id="29314-129">Click **Finish**</span></span>

<span data-ttu-id="29314-130">モデルを編集するため、デザイン サーフェイスを提供するエンティティ デザイナーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="29314-130">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

<span data-ttu-id="29314-131">ウィザードは、次のアクションを実行します。</span><span class="sxs-lookup"><span data-stu-id="29314-131">The wizard performs the following actions:</span></span>

-   <span data-ttu-id="29314-132">概念モデル、ストレージ モデル、およびそれらの間のマッピングを定義する EnumTestModel.edmx ファイルを生成します。</span><span class="sxs-lookup"><span data-stu-id="29314-132">Generates the EnumTestModel.edmx file that defines the conceptual model, the storage model, and the mapping between them.</span></span> <span data-ttu-id="29314-133">生成されたメタデータ ファイルがアセンブリに埋め込まを取得するために、出力アセンブリに埋め込むに .edmx ファイルの Metadata Artifact Processing プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="29314-133">Sets the Metadata Artifact Processing property of the .edmx file to Embed in Output Assembly so the generated metadata files get embedded into the assembly.</span></span>
-   <span data-ttu-id="29314-134">次のアセンブリへの参照を追加します: EntityFramework、System.ComponentModel.DataAnnotations、System.Data.Entity します。</span><span class="sxs-lookup"><span data-stu-id="29314-134">Adds a reference to the following assemblies: EntityFramework, System.ComponentModel.DataAnnotations, and System.Data.Entity.</span></span>
-   <span data-ttu-id="29314-135">EnumTestModel.tt と EnumTestModel.Context.tt ファイルを作成し、.edmx ファイルで追加します。</span><span class="sxs-lookup"><span data-stu-id="29314-135">Creates EnumTestModel.tt and EnumTestModel.Context.tt files and adds them under the .edmx file.</span></span> <span data-ttu-id="29314-136">これらの T4 テンプレート ファイルは、DbContext 派生型と .edmx モデル内のエンティティにマップされる POCO 型を定義するコードを生成します。</span><span class="sxs-lookup"><span data-stu-id="29314-136">These T4 template files generate the code that defines the DbContext derived type and POCO types that map to the entities in the .edmx model.</span></span>

## <a name="add-a-new-entity-type"></a><span data-ttu-id="29314-137">新しいエンティティ型を追加します。</span><span class="sxs-lookup"><span data-stu-id="29314-137">Add a New Entity Type</span></span>

1.  <span data-ttu-id="29314-138">デザイン画面の空の領域を右クリックして**追加 -&gt;エンティティ**、新しいエンティティのダイアログ ボックスが表示されます</span><span class="sxs-lookup"><span data-stu-id="29314-138">Right-click an empty area of the design surface, select **Add -&gt; Entity**, the New Entity dialog box appears</span></span>
2.  <span data-ttu-id="29314-139">指定**部門**型の名前を指定し、指定**DepartmentID**キー プロパティの名前と型をそのまま使用**Int32**</span><span class="sxs-lookup"><span data-stu-id="29314-139">Specify **Department** for the type name and specify **DepartmentID** for the key property name, leave the type as **Int32**</span></span>
3.  <span data-ttu-id="29314-140">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="29314-140">Click **OK**</span></span>
4.  <span data-ttu-id="29314-141">エンティティを右クリックして **- 新しい追加&gt;スカラー プロパティ**</span><span class="sxs-lookup"><span data-stu-id="29314-141">Right-click the entity and select **Add New -&gt; Scalar Property**</span></span>
5.  <span data-ttu-id="29314-142">新しいプロパティの名前を変更**名**</span><span class="sxs-lookup"><span data-stu-id="29314-142">Rename the new property to **Name**</span></span>
6.  <span data-ttu-id="29314-143">新しいプロパティの型を変更**Int32** (既定では、新しいプロパティが文字列型の) 型を変更するプロパティ ウィンドウを開くし、型のプロパティを変更**Int32**</span><span class="sxs-lookup"><span data-stu-id="29314-143">Change the type of the new property to **Int32** (by default, the new property is of String type) To change the type, open the Properties window and change the Type property to **Int32**</span></span>
7.  <span data-ttu-id="29314-144">もう 1 つのスカラー プロパティを追加し、名前を変更する**予算**に種類を変更**10 進数**</span><span class="sxs-lookup"><span data-stu-id="29314-144">Add another scalar property and rename it to **Budget**, change the type to **Decimal**</span></span>

## <a name="add-an-enum-type"></a><span data-ttu-id="29314-145">列挙型を追加します。</span><span class="sxs-lookup"><span data-stu-id="29314-145">Add an Enum Type</span></span>

1.  <span data-ttu-id="29314-146">Entity Framework デザイナーでは、Name プロパティを右クリックして**列挙型に変換**</span><span class="sxs-lookup"><span data-stu-id="29314-146">In the Entity Framework Designer, right-click the Name property, select **Convert to enum**</span></span>

    ![ConvertToEnum](~/ef6/media/converttoenum.png)

2.  <span data-ttu-id="29314-148">**列挙の追加** ダイアログ ボックスに「 **DepartmentNames**列挙型の名前の変更を基になる型**Int32**、型には次メンバーを追加および: 英語、数値演算と経済性</span><span class="sxs-lookup"><span data-stu-id="29314-148">In the **Add Enum** dialog box type **DepartmentNames** for the Enum Type Name, change the Underlying Type to **Int32**, and then add the following members to the type: English, Math, and Economics</span></span>

    ![AddEnumType](~/ef6/media/addenumtype.png)

3.  <span data-ttu-id="29314-150">キーを押して**OK**</span><span class="sxs-lookup"><span data-stu-id="29314-150">Press **OK**</span></span>
4.  <span data-ttu-id="29314-151">モデルを保存し、プロジェクトをビルド</span><span class="sxs-lookup"><span data-stu-id="29314-151">Save the model and build the project</span></span>
    > [!NOTE]
    > <span data-ttu-id="29314-152">ビルドすると、マップされていないエンティティとアソシエーションに関する警告がエラー一覧に表示されます。</span><span class="sxs-lookup"><span data-stu-id="29314-152">When you build, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="29314-153">エラーが解消されますモデルからデータベースを生成するように選択した後にあるために、これらの警告を無視できます。</span><span class="sxs-lookup"><span data-stu-id="29314-153">You can ignore these warnings because after we choose to generate the database from the model, the errors will go away.</span></span>

<span data-ttu-id="29314-154">[プロパティ] ウィンドウを確認することがわかりますに Name プロパティの型が変更されたこと**DepartmentNames**の種類の一覧に新しく追加された列挙型が追加されたとします。</span><span class="sxs-lookup"><span data-stu-id="29314-154">If you look at the Properties window, you will notice that the type of the Name property was changed to **DepartmentNames** and the newly added enum type was added to the list of types.</span></span>

<span data-ttu-id="29314-155">モデル ブラウザー ウィンドウに切り替えた場合、型が列挙型のノードにも追加されたことが表示されます。</span><span class="sxs-lookup"><span data-stu-id="29314-155">If you switch to the Model Browser window, you will see that the type was also added to the Enum Types node.</span></span>

![ModelBrowser](~/ef6/media/modelbrowser.png)

>[!NOTE]
> <span data-ttu-id="29314-157">マウスの右ボタンをクリックして、このウィンドウから新しい列挙型を追加することも**列挙型の追加**します。</span><span class="sxs-lookup"><span data-stu-id="29314-157">You can also add new enum types from this window by clicking the right mouse button and selecting **Add Enum Type**.</span></span> <span data-ttu-id="29314-158">型を作成した後の種類の一覧に表示され、プロパティに関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="29314-158">Once the type is created it will appear in the list of types and you would be able to associate with a property</span></span>

## <a name="generate-database-from-model"></a><span data-ttu-id="29314-159">モデルからデータベースを生成します。</span><span class="sxs-lookup"><span data-stu-id="29314-159">Generate Database from Model</span></span>

<span data-ttu-id="29314-160">モデルに基づいているデータベースを生成できます。</span><span class="sxs-lookup"><span data-stu-id="29314-160">Now we can generate a database that is based on the model.</span></span>

1.  <span data-ttu-id="29314-161">エンティティ デザイナー サーフェスと選択を空の領域を右クリックして**モデルからのデータベースの生成**</span><span class="sxs-lookup"><span data-stu-id="29314-161">Right-click an empty space on the Entity Designer surface and select **Generate Database from Model**</span></span>
2.  <span data-ttu-id="29314-162">データベースの生成ウィザードのデータ接続ダイアログ ボックスが選択の表示 をクリックして、**新しい接続**ボタンを指定する **(localdb)\\mssqllocaldb**サーバー名および**EnumTest** 、データベースとクリック**OK**</span><span class="sxs-lookup"><span data-stu-id="29314-162">The Choose Your Data Connection Dialog Box of the Generate Database Wizard is displayed Click the **New Connection** button Specify **(localdb)\\mssqllocaldb** for the server name and **EnumTest** for the database and click **OK**</span></span>
3.  <span data-ttu-id="29314-163">新しいデータベースを作成するかどうかたずねるダイアログがポップアップ表示をクリックします**はい**します。</span><span class="sxs-lookup"><span data-stu-id="29314-163">A dialog asking if you want to create a new database will pop up, click **Yes**.</span></span>
4.  <span data-ttu-id="29314-164">をクリックして**次**概要、および DDL の定義が含まれていないの設定 ダイアログ ボックス注に表示されますが、データベースを作成するには、生成された DDL をデータベースの作成ウィザードがデータ定義言語 (DDL) を生成し、列挙型にマップされるテーブル</span><span class="sxs-lookup"><span data-stu-id="29314-164">Click **Next** and the Create Database Wizard generates data definition language (DDL) for creating a database The generated DDL is displayed in the Summary and Settings Dialog Box Note, that the DDL does not contain a definition for a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="29314-165">クリックして**完了**完了 をクリックしても、DDL スクリプトは実行されません。</span><span class="sxs-lookup"><span data-stu-id="29314-165">Click **Finish** Clicking Finish does not execute the DDL script.</span></span>
6.  <span data-ttu-id="29314-166">データベースの作成ウィザードは、次: 開きます、 **EnumTest.edmx.sql** T-SQL エディターの生成で EDMX のストアのスキーマとマッピング セクションでは App.config ファイルに追加の接続文字列情報をファイル</span><span class="sxs-lookup"><span data-stu-id="29314-166">The Create Database Wizard does the following: Opens the **EnumTest.edmx.sql** in T-SQL Editor Generates the store schema and mapping sections of the EDMX file Adds connection string information to the App.config file</span></span>
7.  <span data-ttu-id="29314-167">T-SQL エディターでマウスの右ボタンをクリックし、選択**Execute**サーバー ダイアログへの接続が表示されたら、手順 2. の接続情報を入力し、クリックして**接続**</span><span class="sxs-lookup"><span data-stu-id="29314-167">Click the right mouse button in T-SQL Editor and select **Execute** The Connect to Server dialog appears, enter the connection information from step 2 and click **Connect**</span></span>
8.  <span data-ttu-id="29314-168">生成されたスキーマを表示するには、SQL Server オブジェクト エクスプ ローラーでデータベース名を右クリックして**更新**</span><span class="sxs-lookup"><span data-stu-id="29314-168">To view the generated schema, right-click on the database name in SQL Server Object Explorer and select **Refresh**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="29314-169">永続化およびデータの取得</span><span class="sxs-lookup"><span data-stu-id="29314-169">Persist and Retrieve Data</span></span>

<span data-ttu-id="29314-170">Main メソッドが定義されている Program.cs ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="29314-170">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="29314-171">Main 関数には、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="29314-171">Add the following code into the Main function.</span></span> <span data-ttu-id="29314-172">コードでは、コンテキストに新しい部門オブジェクトを追加します。</span><span class="sxs-lookup"><span data-stu-id="29314-172">The code adds a new Department object to the context.</span></span> <span data-ttu-id="29314-173">データが保存されます。</span><span class="sxs-lookup"><span data-stu-id="29314-173">It then saves the data.</span></span> <span data-ttu-id="29314-174">コードでは、名前は DepartmentNames.English 部門を返す LINQ クエリも実行します。</span><span class="sxs-lookup"><span data-stu-id="29314-174">The code also executes a LINQ query that returns a Department where the name is DepartmentNames.English.</span></span>

``` csharp
using (var context = new EnumTestModelContainer())
{
    context.Departments.Add(new Department{ Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} and Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

<span data-ttu-id="29314-175">アプリケーションをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="29314-175">Compile and run the application.</span></span> <span data-ttu-id="29314-176">このプログラムの出力は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="29314-176">The program produces the following output:</span></span>

```
DepartmentID: 1 Name: English
```

<span data-ttu-id="29314-177">データベースにデータを表示するには、SQL Server オブジェクト エクスプ ローラーでデータベース名を右クリックして**更新**します。</span><span class="sxs-lookup"><span data-stu-id="29314-177">To view data in the database, right-click on the database name in SQL Server Object Explorer and select **Refresh**.</span></span> <span data-ttu-id="29314-178">マウスの右ボタンをクリックし、テーブルをクリックし、**ビュー データ**します。</span><span class="sxs-lookup"><span data-stu-id="29314-178">Then, click the right mouse button on the table and select **View Data**.</span></span>

## <a name="summary"></a><span data-ttu-id="29314-179">まとめ</span><span class="sxs-lookup"><span data-stu-id="29314-179">Summary</span></span>

<span data-ttu-id="29314-180">このチュートリアルでは、Entity Framework デザイナーを使用して列挙型にマップする方法とコードで列挙型を使用する方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="29314-180">In this walkthrough we looked at how to map enum types using the Entity Framework Designer and how to use enums in code.</span></span> 
