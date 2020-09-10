---
title: Enum サポート-EF デザイナー-EF6
description: Enum Support-Entity Framework 6 の EF デザイナー
author: divega
ms.date: 10/23/2016
ms.assetid: c6ae6d8f-1ace-47db-ad47-b1718f1ba082
uid: ef6/modeling/designer/data-types/enums
ms.openlocfilehash: 316e8160cfd69d9912d63260471eee3df0eb58f6
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620512"
---
# <a name="enum-support---ef-designer"></a><span data-ttu-id="ceda8-103">列挙型のサポート-EF デザイナー</span><span class="sxs-lookup"><span data-stu-id="ceda8-103">Enum Support - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="ceda8-104">**EF5** 以降: このページで説明した機能、api などは、Entity Framework 5 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="ceda8-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="ceda8-105">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="ceda8-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="ceda8-106">このビデオとステップバイステップのチュートリアルでは、Entity Framework Designer で列挙型を使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-106">This video and step-by-step walkthrough shows how to use enum types with the Entity Framework Designer.</span></span> <span data-ttu-id="ceda8-107">また、LINQ クエリで列挙型を使用する方法も示します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-107">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="ceda8-108">このチュートリアルでは Model First を使用して新しいデータベースを作成しますが、既存のデータベースにマップするには、EF Designer を [Database First](xref:ef6/modeling/designer/workflows/database-first) ワークフローと共に使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="ceda8-108">This walkthrough will use Model First to create a new database, but the EF Designer can also be used with the [Database First](xref:ef6/modeling/designer/workflows/database-first) workflow to map to an existing database.</span></span>

<span data-ttu-id="ceda8-109">Enum サポートは Entity Framework 5 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="ceda8-109">Enum support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="ceda8-110">列挙型、空間データ型、テーブル値関数などの新機能を使用するには、.NET Framework 4.5 を対象にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ceda8-110">To use the new features like enums, spatial data types, and table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="ceda8-111">既定では、Visual Studio 2012 は .NET 4.5 を対象としています。</span><span class="sxs-lookup"><span data-stu-id="ceda8-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="ceda8-112">Entity Framework では、列挙体には、 **Byte**、 **Int16**、 **Int32**、 **Int64** 、または **SByte**の各基になる型を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="ceda8-112">In Entity Framework, an enumeration can have the following underlying types: **Byte**, **Int16**, **Int32**, **Int64** , or **SByte**.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="ceda8-113">ビデオを見る</span><span class="sxs-lookup"><span data-stu-id="ceda8-113">Watch the Video</span></span>
<span data-ttu-id="ceda8-114">このビデオでは、Entity Framework Designer で列挙型を使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-114">This video shows how to use enum types with the Entity Framework Designer.</span></span> <span data-ttu-id="ceda8-115">また、LINQ クエリで列挙型を使用する方法も示します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-115">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="ceda8-116">**表示者**: ジュリアつい</span><span class="sxs-lookup"><span data-stu-id="ceda8-116">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="ceda8-117">**ビデオ**: [WMV](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv)  |  [MP4](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v)  |  [WMV (ZIP)](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)</span><span class="sxs-lookup"><span data-stu-id="ceda8-117">**Video**: [WMV](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv) | [MP4](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="ceda8-118">前提条件</span><span class="sxs-lookup"><span data-stu-id="ceda8-118">Pre-Requisites</span></span>

<span data-ttu-id="ceda8-119">このチュートリアルを完了するには、Visual Studio 2012、Ultimate、Premium、Professional、または Web Express エディションがインストールされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="ceda8-119">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="ceda8-120">プロジェクトを設定する</span><span class="sxs-lookup"><span data-stu-id="ceda8-120">Set up the Project</span></span>

1.  <span data-ttu-id="ceda8-121">Visual Studio 2012 を開く</span><span class="sxs-lookup"><span data-stu-id="ceda8-121">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="ceda8-122">[**ファイル**] メニューの [**新規作成**] をポイントし、[**プロジェクト**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="ceda8-122">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="ceda8-123">左側のウィンドウで、[ **Visual C \# **] をクリックし、**コンソール**テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-123">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="ceda8-124">プロジェクトの名前として「 **Enumefdesigner** 」と入力し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="ceda8-124">Enter **EnumEFDesigner** as the name of the project and click **OK**</span></span>

## <a name="create-a-new-model-using-the-ef-designer"></a><span data-ttu-id="ceda8-125">EF デザイナーを使用して新しいモデルを作成する</span><span class="sxs-lookup"><span data-stu-id="ceda8-125">Create a New Model using the EF Designer</span></span>

1.  <span data-ttu-id="ceda8-126">ソリューションエクスプローラーでプロジェクト名を右クリックし、[**追加**] をポイントして、[**新しい項目**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="ceda8-126">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="ceda8-127">左側のメニューから [ **データ** ] を選択し、[テンプレート] ペインで [ **ADO.NET Entity Data Model** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-127">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane</span></span>
3.  <span data-ttu-id="ceda8-128">ファイル名として「 **Enumtestmodel. .edmx** 」と入力し、[**追加**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="ceda8-128">Enter **EnumTestModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="ceda8-129">Entity Data Model ウィザードページで、[モデルのコンテンツの選択] ダイアログボックスの [ **空のモデル** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-129">On the Entity Data Model Wizard page, select **Empty Model** in the Choose Model Contents dialog box</span></span>
5.  <span data-ttu-id="ceda8-130">**[完了]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="ceda8-130">Click **Finish**</span></span>

<span data-ttu-id="ceda8-131">モデルを編集するためのデザイン画面を提供する Entity Designer が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ceda8-131">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

<span data-ttu-id="ceda8-132">ウィザードは、次のアクションを実行します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-132">The wizard performs the following actions:</span></span>

-   <span data-ttu-id="ceda8-133">概念モデル、ストレージモデル、およびそれらの間のマッピングを定義する EnumTestModel .edmx ファイルを生成します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-133">Generates the EnumTestModel.edmx file that defines the conceptual model, the storage model, and the mapping between them.</span></span> <span data-ttu-id="ceda8-134">生成されたメタデータファイルがアセンブリに埋め込まれるように、出力アセンブリに埋め込む .edmx ファイルのメタデータ成果物の処理プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-134">Sets the Metadata Artifact Processing property of the .edmx file to Embed in Output Assembly so the generated metadata files get embedded into the assembly.</span></span>
-   <span data-ttu-id="ceda8-135">次のアセンブリへの参照を追加します。 EntityFramework、System.componentmodel、および system.object です。</span><span class="sxs-lookup"><span data-stu-id="ceda8-135">Adds a reference to the following assemblies: EntityFramework, System.ComponentModel.DataAnnotations, and System.Data.Entity.</span></span>
-   <span data-ttu-id="ceda8-136">EnumTestModel.tt ファイルと EnumTestModel.Context.tt ファイルを作成し、.edmx ファイルの下に追加します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-136">Creates EnumTestModel.tt and EnumTestModel.Context.tt files and adds them under the .edmx file.</span></span> <span data-ttu-id="ceda8-137">これらの T4 テンプレートファイルは、.edmx モデル内のエンティティにマップされる DbContext の派生型と POCO 型を定義するコードを生成します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-137">These T4 template files generate the code that defines the DbContext derived type and POCO types that map to the entities in the .edmx model.</span></span>

## <a name="add-a-new-entity-type"></a><span data-ttu-id="ceda8-138">新しいエンティティ型の追加</span><span class="sxs-lookup"><span data-stu-id="ceda8-138">Add a New Entity Type</span></span>

1.  <span data-ttu-id="ceda8-139">デザイン画面の空の領域を右クリックし、[ \*\* &gt; エンティティの追加\*\*] を選択すると、[新しいエンティティ] ダイアログボックスが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ceda8-139">Right-click an empty area of the design surface, select **Add -&gt; Entity**, the New Entity dialog box appears</span></span>
2.  <span data-ttu-id="ceda8-140">型名に**Department**を指定し、キープロパティ名として**DepartmentID**を指定します。型は**Int32**型のままにします。</span><span class="sxs-lookup"><span data-stu-id="ceda8-140">Specify **Department** for the type name and specify **DepartmentID** for the key property name, leave the type as **Int32**</span></span>
3.  <span data-ttu-id="ceda8-141">**[OK]**</span><span class="sxs-lookup"><span data-stu-id="ceda8-141">Click **OK**</span></span>
4.  <span data-ttu-id="ceda8-142">エンティティを右クリックし、[**新しい &gt; スカラープロパティの追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-142">Right-click the entity and select **Add New -&gt; Scalar Property**</span></span>
5.  <span data-ttu-id="ceda8-143">新しいプロパティの名前を**Name**に変更します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-143">Rename the new property to **Name**</span></span>
6.  <span data-ttu-id="ceda8-144">新しいプロパティの型を**int32**に変更し (既定では、新しいプロパティは文字列型です)、型を変更し、プロパティウィンドウを開いて、type プロパティを**int32**に変更します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-144">Change the type of the new property to **Int32** (by default, the new property is of String type) To change the type, open the Properties window and change the Type property to **Int32**</span></span>
7.  <span data-ttu-id="ceda8-145">別のスカラープロパティを追加して、それを**Budget**に変更し、型を**Decimal**に変更します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-145">Add another scalar property and rename it to **Budget**, change the type to **Decimal**</span></span>

## <a name="add-an-enum-type"></a><span data-ttu-id="ceda8-146">列挙型の追加</span><span class="sxs-lookup"><span data-stu-id="ceda8-146">Add an Enum Type</span></span>

1.  <span data-ttu-id="ceda8-147">Entity Framework Designer で、[名前] プロパティを右クリックし、[**列挙型への変換**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-147">In the Entity Framework Designer, right-click the Name property, select **Convert to enum**</span></span>

    ![列挙型への変換](~/ef6/media/converttoenum.png)

2.  <span data-ttu-id="ceda8-149">[列挙型の **追加** ] ダイアログボックスで、列挙型の名前として「 **DepartmentNames** 」と入力し、基になる型を **Int32**に変更してから、次のメンバーを型に追加します。英語、数学、および経済性</span><span class="sxs-lookup"><span data-stu-id="ceda8-149">In the **Add Enum** dialog box type **DepartmentNames** for the Enum Type Name, change the Underlying Type to **Int32**, and then add the following members to the type: English, Math, and Economics</span></span>

    ![列挙型の追加](~/ef6/media/addenumtype.png)

3.  <span data-ttu-id="ceda8-151">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="ceda8-151">Press **OK**</span></span>
4.  <span data-ttu-id="ceda8-152">モデルを保存してプロジェクトをビルドする</span><span class="sxs-lookup"><span data-stu-id="ceda8-152">Save the model and build the project</span></span>
    > [!NOTE]
    > <span data-ttu-id="ceda8-153">ビルドすると、マップされていないエンティティとアソシエーションに関する警告がエラー一覧に表示されることがあります。</span><span class="sxs-lookup"><span data-stu-id="ceda8-153">When you build, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="ceda8-154">モデルからデータベースを生成することを選択した後、エラーが解消されるため、これらの警告は無視してかまいません。</span><span class="sxs-lookup"><span data-stu-id="ceda8-154">You can ignore these warnings because after we choose to generate the database from the model, the errors will go away.</span></span>

<span data-ttu-id="ceda8-155">プロパティウィンドウを見ると、Name プロパティの型が **DepartmentNames** に変更され、新しく追加された列挙型が型のリストに追加されたことがわかります。</span><span class="sxs-lookup"><span data-stu-id="ceda8-155">If you look at the Properties window, you will notice that the type of the Name property was changed to **DepartmentNames** and the newly added enum type was added to the list of types.</span></span>

<span data-ttu-id="ceda8-156">[モデルブラウザー] ウィンドウに切り替えた場合は、型が [列挙型] ノードにも追加されていることがわかります。</span><span class="sxs-lookup"><span data-stu-id="ceda8-156">If you switch to the Model Browser window, you will see that the type was also added to the Enum Types node.</span></span>

![モデルブラウザー](~/ef6/media/modelbrowser.png)

>[!NOTE]
> <span data-ttu-id="ceda8-158">マウスの右ボタンをクリックし、[ **列挙型の追加**] を選択して、このウィンドウから新しい列挙型を追加することもできます。</span><span class="sxs-lookup"><span data-stu-id="ceda8-158">You can also add new enum types from this window by clicking the right mouse button and selecting **Add Enum Type**.</span></span> <span data-ttu-id="ceda8-159">型が作成されると、型のリストに表示され、プロパティに関連付けることができるようになります。</span><span class="sxs-lookup"><span data-stu-id="ceda8-159">Once the type is created it will appear in the list of types and you would be able to associate with a property</span></span>

## <a name="generate-database-from-model"></a><span data-ttu-id="ceda8-160">モデルからデータベースを生成</span><span class="sxs-lookup"><span data-stu-id="ceda8-160">Generate Database from Model</span></span>

<span data-ttu-id="ceda8-161">これで、モデルに基づくデータベースを生成できます。</span><span class="sxs-lookup"><span data-stu-id="ceda8-161">Now we can generate a database that is based on the model.</span></span>

1.  <span data-ttu-id="ceda8-162">Entity Designer 画面の空の領域を右クリックし、[**モデルからデータベースを生成**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-162">Right-click an empty space on the Entity Designer surface and select **Generate Database from Model**</span></span>
2.  <span data-ttu-id="ceda8-163">データベース生成ウィザードの [データ接続の選択] ダイアログボックスが表示されたら、[**新しい接続**] ボタンをクリックします。 [サーバー名] に **「(localdb) \\ Mssqllocaldb** 、データベースに**Enumtest**を指定し、[ **OK** ] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="ceda8-163">The Choose Your Data Connection Dialog Box of the Generate Database Wizard is displayed Click the **New Connection** button Specify **(localdb)\\mssqllocaldb** for the server name and **EnumTest** for the database and click **OK**</span></span>
3.  <span data-ttu-id="ceda8-164">新しいデータベースを作成するかどうかを確認するダイアログボックスが表示されたら、[ **はい**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="ceda8-164">A dialog asking if you want to create a new database will pop up, click **Yes**.</span></span>
4.  <span data-ttu-id="ceda8-165">[ **次へ** ] をクリックすると、データベースの作成ウィザードによって、生成された ddl がデータベースを作成するためのデータ定義言語 (ddl) が生成されます。 [概要と設定] ダイアログボックスには、ddl に列挙型にマップされているテーブルの定義が含まれていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-165">Click **Next** and the Create Database Wizard generates data definition language (DDL) for creating a database The generated DDL is displayed in the Summary and Settings Dialog Box Note, that the DDL does not contain a definition for a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="ceda8-166">[ **完了** ] をクリックすると、DDL スクリプトは実行されません。</span><span class="sxs-lookup"><span data-stu-id="ceda8-166">Click **Finish** Clicking Finish does not execute the DDL script.</span></span>
6.  <span data-ttu-id="ceda8-167">データベースの作成ウィザードでは、次のことが行われます。 T-sql エディターで **Enumtest. .edmx. sql** が生成され、edmx ファイルのストアスキーマとマッピングセクションによって、App.config ファイルに接続文字列情報が追加されます。</span><span class="sxs-lookup"><span data-stu-id="ceda8-167">The Create Database Wizard does the following: Opens the **EnumTest.edmx.sql** in T-SQL Editor Generates the store schema and mapping sections of the EDMX file Adds connection string information to the App.config file</span></span>
7.  <span data-ttu-id="ceda8-168">T-sql エディターでマウスの右ボタンをクリック**し、[サーバーへの接続**] ダイアログボックスが表示されたら、手順2の接続情報を入力して [**接続**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="ceda8-168">Click the right mouse button in T-SQL Editor and select **Execute** The Connect to Server dialog appears, enter the connection information from step 2 and click **Connect**</span></span>
8.  <span data-ttu-id="ceda8-169">生成されたスキーマを表示するには、SQL Server オブジェクトエクスプローラーでデータベース名を右クリックし、[最新の状態に**更新**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-169">To view the generated schema, right-click on the database name in SQL Server Object Explorer and select **Refresh**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="ceda8-170">データの永続化と取得</span><span class="sxs-lookup"><span data-stu-id="ceda8-170">Persist and Retrieve Data</span></span>

<span data-ttu-id="ceda8-171">Main メソッドが定義されている Program.cs ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="ceda8-171">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="ceda8-172">Main 関数に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-172">Add the following code into the Main function.</span></span> <span data-ttu-id="ceda8-173">このコードは、新しい Department オブジェクトをコンテキストに追加します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-173">The code adds a new Department object to the context.</span></span> <span data-ttu-id="ceda8-174">その後、データを保存します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-174">It then saves the data.</span></span> <span data-ttu-id="ceda8-175">また、このコードでは、名前が DepartmentNames である Department を返す LINQ クエリも実行されます。</span><span class="sxs-lookup"><span data-stu-id="ceda8-175">The code also executes a LINQ query that returns a Department where the name is DepartmentNames.English.</span></span>

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

<span data-ttu-id="ceda8-176">アプリケーションをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-176">Compile and run the application.</span></span> <span data-ttu-id="ceda8-177">このプログラムの出力は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="ceda8-177">The program produces the following output:</span></span>

```console
DepartmentID: 1 Name: English
```

<span data-ttu-id="ceda8-178">データベースのデータを表示するには、SQL Server オブジェクトエクスプローラーでデータベース名を右クリックし、[最新の情報に **更新**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-178">To view data in the database, right-click on the database name in SQL Server Object Explorer and select **Refresh**.</span></span> <span data-ttu-id="ceda8-179">次に、テーブルのマウスの右ボタンをクリックし、[ **データの表示**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="ceda8-179">Then, click the right mouse button on the table and select **View Data**.</span></span>

## <a name="summary"></a><span data-ttu-id="ceda8-180">要約</span><span class="sxs-lookup"><span data-stu-id="ceda8-180">Summary</span></span>

<span data-ttu-id="ceda8-181">このチュートリアルでは、Entity Framework Designer を使用して列挙型をマップする方法と、コードで enum を使用する方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="ceda8-181">In this walkthrough we looked at how to map enum types using the Entity Framework Designer and how to use enums in code.</span></span> 
