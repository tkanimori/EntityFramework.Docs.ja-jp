---
title: WPF を使用したデータバインド-EF6
author: divega
ms.date: 05/19/2020
ms.assetid: e90d48e6-bea7785-47ef-b756-7b89cce4daf0
ms.openlocfilehash: baeb75b1ee386ca58013048bcc31ea4074604673
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526850"
---
# <a name="databinding-with-wpf"></a><span data-ttu-id="df468-102">WPF とのデータバインド</span><span class="sxs-lookup"><span data-stu-id="df468-102">Databinding with WPF</span></span>

> [!IMPORTANT]
> <span data-ttu-id="df468-103">**このドキュメントは、.NET Framework の WPF でのみ有効です**</span><span class="sxs-lookup"><span data-stu-id="df468-103">**This document is valid for WPF on the .NET Framework only**</span></span>
>
> <span data-ttu-id="df468-104">このドキュメントでは、.NET Framework での WPF のデータバインドについて説明します。</span><span class="sxs-lookup"><span data-stu-id="df468-104">This document describes databinding for WPF on the .NET Framework.</span></span> <span data-ttu-id="df468-105">新しい .NET Core プロジェクトの場合は、Entity Framework 6 ではなく[EF Core](/ef/core)を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="df468-105">For new .NET Core projects, we recommend you use [EF Core](/ef/core) instead of Entity Framework 6.</span></span> <span data-ttu-id="df468-106">EF Core でのデータバインドのドキュメントは、「 [WPF でのはじめに](/ef/core/get-started/wpf)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="df468-106">The documentation for databinding in EF Core is here: [Getting Started with WPF](/ef/core/get-started/wpf).</span></span>

<span data-ttu-id="df468-107">このステップバイステップチュートリアルでは、"マスター/詳細" フォームで POCO 型を WPF コントロールにバインドする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="df468-107">This step-by-step walkthrough shows how to bind POCO types to WPF controls in a “master-detail" form.</span></span> <span data-ttu-id="df468-108">アプリケーションは、Entity Framework Api を使用して、データベースのデータをオブジェクトに読み込み、変更を追跡し、データベースにデータを保持します。</span><span class="sxs-lookup"><span data-stu-id="df468-108">The application uses the Entity Framework APIs to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="df468-109">このモデルでは、1対多のリレーションシップに参加する2つの型が定義されています。**カテゴリ**(プリンシパル \\ マスター) と**製品**(依存関係の \\ 詳細) です。</span><span class="sxs-lookup"><span data-stu-id="df468-109">The model defines two types that participate in one-to-many relationship: **Category** (principal\\master) and **Product** (dependent\\detail).</span></span> <span data-ttu-id="df468-110">次に、Visual Studio ツールを使用して、モデルで定義されている型を WPF コントロールにバインドします。</span><span class="sxs-lookup"><span data-stu-id="df468-110">Then, the Visual Studio tools are used to bind the types defined in the model to the WPF controls.</span></span> <span data-ttu-id="df468-111">WPF データバインディングフレームワークを使用すると、関連するオブジェクト間のナビゲーションが可能になります。マスタービューで行を選択すると、対応する子データで詳細ビューが更新されます。</span><span class="sxs-lookup"><span data-stu-id="df468-111">The WPF data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="df468-112">このチュートリアルのスクリーンショットとコードリストは Visual Studio 2013 から取得されていますが、Visual Studio 2012 または Visual Studio 2010 を使用してこのチュートリアルを完了することができます。</span><span class="sxs-lookup"><span data-stu-id="df468-112">The screen shots and code listings in this walkthrough are taken from Visual Studio 2013 but you can complete this walkthrough with Visual Studio 2012 or Visual Studio 2010.</span></span>

## <a name="use-the-object-option-for-creating-wpf-data-sources"></a><span data-ttu-id="df468-113">WPF データソースを作成するには、' Object ' オプションを使用します</span><span class="sxs-lookup"><span data-stu-id="df468-113">Use the 'Object' Option for Creating WPF Data Sources</span></span>

<span data-ttu-id="df468-114">以前のバージョンの Entity Framework では、EF デザイナーで作成されたモデルに基づいて新しいデータソースを作成するときに、**データベース**オプションを使用することをお勧めしました。</span><span class="sxs-lookup"><span data-stu-id="df468-114">With previous version of Entity Framework we used to recommend using the **Database** option when creating a new Data Source based on a model created with the EF Designer.</span></span> <span data-ttu-id="df468-115">これは、デザイナーが ObjectContext および EntityObject から派生したエンティティクラスから派生したコンテキストを生成するためです。</span><span class="sxs-lookup"><span data-stu-id="df468-115">This was because the designer would generate a context that derived from ObjectContext and entity classes that derived from EntityObject.</span></span> <span data-ttu-id="df468-116">データベースオプションを使用すると、この API サーフェイスと対話するための最適なコードを記述するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="df468-116">Using the Database option would help you write the best code for interacting with this API surface.</span></span>

<span data-ttu-id="df468-117">Visual Studio 2012 および Visual Studio 2013 用の EF デザイナーは、単純な POCO エンティティクラスと共に DbContext から派生するコンテキストを生成します。</span><span class="sxs-lookup"><span data-stu-id="df468-117">The EF Designers for Visual Studio 2012 and Visual Studio 2013 generate a context that derives from DbContext together with simple POCO entity classes.</span></span> <span data-ttu-id="df468-118">Visual Studio 2010 では、このチュートリアルの後半で説明するように、DbContext を使用するコード生成テンプレートにスワップすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="df468-118">With Visual Studio 2010 we recommend swapping to a code generation template that uses DbContext as described later in this walkthrough.</span></span>

<span data-ttu-id="df468-119">DbContext API サーフェイスを使用する場合は、このチュートリアルで示すように、新しいデータソースを作成するときに**オブジェクト**オプションを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="df468-119">When using the DbContext API surface you should use the **Object** option when creating a new Data Source, as shown in this walkthrough.</span></span>

<span data-ttu-id="df468-120">必要に応じて、EF デザイナーで作成されたモデルの[ObjectContext ベースのコード生成に戻す](~/ef6/modeling/designer/codegen/legacy-objectcontext.md)ことができます。</span><span class="sxs-lookup"><span data-stu-id="df468-120">If needed, you can [revert to ObjectContext based code generation](~/ef6/modeling/designer/codegen/legacy-objectcontext.md) for models created with the EF Designer.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="df468-121">前提条件</span><span class="sxs-lookup"><span data-stu-id="df468-121">Pre-Requisites</span></span>

<span data-ttu-id="df468-122">このチュートリアルを完了するには、Visual Studio 2013、Visual Studio 2012、または Visual Studio 2010 がインストールされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="df468-122">You need to have Visual Studio 2013, Visual Studio 2012 or Visual Studio 2010 installed to complete this walkthrough.</span></span>

<span data-ttu-id="df468-123">Visual Studio 2010 を使用している場合は、NuGet もインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="df468-123">If you are using Visual Studio 2010, you also have to install NuGet.</span></span> <span data-ttu-id="df468-124">詳細については、「 [NuGet のインストール](https://docs.microsoft.com/nuget/install-nuget-client-tools)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="df468-124">For more information, see [Installing NuGet](https://docs.microsoft.com/nuget/install-nuget-client-tools).</span></span>  

## <a name="create-the-application"></a><span data-ttu-id="df468-125">アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="df468-125">Create the Application</span></span>

-   <span data-ttu-id="df468-126">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="df468-126">Open Visual Studio</span></span>
-   <span data-ttu-id="df468-127">**ファイル- &gt; 新規 &gt; プロジェクト....**</span><span class="sxs-lookup"><span data-stu-id="df468-127">**File -&gt; New -&gt; Project….**</span></span>
-   <span data-ttu-id="df468-128">左側のウィンドウで [ **Windows**] を選択し、   右側のウィンドウで **WPFApplication**を選択します。</span><span class="sxs-lookup"><span data-stu-id="df468-128">Select **Windows** in the left pane and **WPFApplication** in the right pane</span></span>
-   <span data-ttu-id="df468-129">名前として「 **WPFwithEFSample**」と入力し   ます。</span><span class="sxs-lookup"><span data-stu-id="df468-129">Enter **WPFwithEFSample** as the name</span></span>
-   <span data-ttu-id="df468-130">[ **OK]** を選択</span><span class="sxs-lookup"><span data-stu-id="df468-130">Select **OK**</span></span>

## <a name="install-the-entity-framework-nuget-package"></a><span data-ttu-id="df468-131">Entity Framework NuGet パッケージをインストールする</span><span class="sxs-lookup"><span data-stu-id="df468-131">Install the Entity Framework NuGet package</span></span>

-   <span data-ttu-id="df468-132">ソリューションエクスプローラーで、 **Winフォーム Withefsample**プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="df468-132">In Solution Explorer, right-click on the **WinFormswithEFSample** project</span></span>
-   <span data-ttu-id="df468-133">[ **NuGet パッケージの管理...** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="df468-133">Select **Manage NuGet Packages…**</span></span>
-   <span data-ttu-id="df468-134">[NuGet パッケージの管理] ダイアログで、[**オンライン**] タブを選択し、 **entityframework**パッケージを選択します。</span><span class="sxs-lookup"><span data-stu-id="df468-134">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
-   <span data-ttu-id="df468-135">**[Install]** (インストール) をクリックします。</span><span class="sxs-lookup"><span data-stu-id="df468-135">Click **Install**</span></span>  
    >[!NOTE]
    > <span data-ttu-id="df468-136">EntityFramework アセンブリに加えて、System.componentmodel への参照も追加されます。</span><span class="sxs-lookup"><span data-stu-id="df468-136">In addition to the EntityFramework assembly a reference to System.ComponentModel.DataAnnotations is also added.</span></span> <span data-ttu-id="df468-137">プロジェクトに system.object への参照が含まれている場合は、EntityFramework パッケージのインストール時に削除されます。</span><span class="sxs-lookup"><span data-stu-id="df468-137">If the project has a reference to System.Data.Entity, then it will be removed when the EntityFramework package is installed.</span></span> <span data-ttu-id="df468-138">Entity Framework 6 アプリケーションでは、system.object アセンブリは使用されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="df468-138">The System.Data.Entity assembly is no longer used for Entity Framework 6 applications.</span></span>

## <a name="define-a-model"></a><span data-ttu-id="df468-139">モデルの定義</span><span class="sxs-lookup"><span data-stu-id="df468-139">Define a Model</span></span>

<span data-ttu-id="df468-140">このチュートリアルでは、Code First または EF デザイナーを使用してモデルを実装することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="df468-140">In this walkthrough you can chose to implement a model using Code First or the EF Designer.</span></span> <span data-ttu-id="df468-141">次の2つのセクションのいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="df468-141">Complete one of the two following sections.</span></span>

### <a name="option-1-define-a-model-using-code-first"></a><span data-ttu-id="df468-142">オプション 1: Code First を使用してモデルを定義する</span><span class="sxs-lookup"><span data-stu-id="df468-142">Option 1: Define a Model using Code First</span></span>

<span data-ttu-id="df468-143">このセクションでは、Code First を使用してモデルとそれに関連付けられたデータベースを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="df468-143">This section shows how to create a model and its associated database using Code First.</span></span> <span data-ttu-id="df468-144">EF デザイナーを使用してデータベースからモデルをリバースエンジニアリングする場合に Database First を使用する場合は、次のセクション (「**オプション 2: Database First を使用してモデルを定義**する」) に進んでください。</span><span class="sxs-lookup"><span data-stu-id="df468-144">Skip to the next section (**Option 2: Define a model using Database First)** if you would rather use Database First to reverse engineer your model from a database using the EF designer</span></span>

<span data-ttu-id="df468-145">Code First 開発を使用する場合は、通常、概念 (ドメイン) モデルを定義する .NET Framework クラスを記述することから始めます。</span><span class="sxs-lookup"><span data-stu-id="df468-145">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span>

-   <span data-ttu-id="df468-146">WPFwithEFSample に新しいクラスを追加し **ます。**</span><span class="sxs-lookup"><span data-stu-id="df468-146">Add a new class to the **WPFwithEFSample:**</span></span>
    -   <span data-ttu-id="df468-147">プロジェクト名を右クリックします。</span><span class="sxs-lookup"><span data-stu-id="df468-147">Right-click on the project name</span></span>
    -   <span data-ttu-id="df468-148">[**追加**]、[**新しい項目**] の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="df468-148">Select **Add**, then **New Item**</span></span>
    -   <span data-ttu-id="df468-149">[**クラス**] を選択し、[クラス名] に「 **Product**」と入力し   ます。</span><span class="sxs-lookup"><span data-stu-id="df468-149">Select **Class** and enter **Product** for the class name</span></span>
-   <span data-ttu-id="df468-150"> **Product**   クラスの定義を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="df468-150">Replace the **Product** class definition with the following code:</span></span>

``` csharp
    namespace WPFwithEFSample
    {
        public class Product
        {
            public int ProductId { get; set; }
            public string Name { get; set; }

            public int CategoryId { get; set; }
            public virtual Category Category { get; set; }
        }
    }

-   Add a **Category** class with the following definition:

    using System.Collections.ObjectModel;

    namespace WPFwithEFSample
    {
        public class Category
        {
            public Category()
            {
                this.Products = new ObservableCollection<Product>();
            }

            public int CategoryId { get; set; }
            public string Name { get; set; }

            public virtual ObservableCollection<Product> Products { get; private set; }
        }
    }
```

<span data-ttu-id="df468-151">**Product**クラスの**Category**クラスおよび**category**プロパティの**Products**プロパティは、ナビゲーションプロパティです。</span><span class="sxs-lookup"><span data-stu-id="df468-151">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="df468-152">Entity Framework では、ナビゲーションプロパティを使用して、2つのエンティティ型間のリレーションシップをナビゲートすることができます。</span><span class="sxs-lookup"><span data-stu-id="df468-152">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="df468-153">エンティティを定義するだけでなく、DbContext から派生し、Dbcontext TEntity プロパティを公開するクラスを定義する必要があり &lt; &gt; ます。</span><span class="sxs-lookup"><span data-stu-id="df468-153">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="df468-154">DbSet TEntity プロパティを使用すると、 &lt; &gt; モデルに含める型をコンテキストに認識させることができます。</span><span class="sxs-lookup"><span data-stu-id="df468-154">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="df468-155">DbContext の派生型のインスタンスは、実行時にエンティティオブジェクトを管理します。これには、データベースからのデータを使用したオブジェクトの読み込み、変更の追跡、およびデータベースへのデータの永続化が含まれます。</span><span class="sxs-lookup"><span data-stu-id="df468-155">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

-   <span data-ttu-id="df468-156">次の定義を使用して、プロジェクトに新しい**Productcontext**クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="df468-156">Add a new **ProductContext** class to the project with the following definition:</span></span>

``` csharp
    using System.Data.Entity;

    namespace WPFwithEFSample
    {
        public class ProductContext : DbContext
        {
            public DbSet<Category> Categories { get; set; }
            public DbSet<Product> Products { get; set; }
        }
    }
```

<span data-ttu-id="df468-157">プロジェクトをコンパイルします。</span><span class="sxs-lookup"><span data-stu-id="df468-157">Compile the project.</span></span>

### <a name="option-2-define-a-model-using-database-first"></a><span data-ttu-id="df468-158">オプション 2: Database First を使用してモデルを定義する</span><span class="sxs-lookup"><span data-stu-id="df468-158">Option 2: Define a model using Database First</span></span>

<span data-ttu-id="df468-159">このセクションでは、Database First を使用して、EF デザイナーを使用してデータベースからモデルをリバースエンジニアリングする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="df468-159">This section shows how to use Database First to reverse engineer your model from a database using the EF designer.</span></span> <span data-ttu-id="df468-160">前のセクション (**オプション 1: Code First を使用してモデルを定義する)** を完了している場合は、このセクションをスキップし、「**遅延読み込み**」セクションに直接移動します。</span><span class="sxs-lookup"><span data-stu-id="df468-160">If you completed the previous section (**Option 1: Define a model using Code First)**, then skip this section and go straight to the **Lazy Loading** section.</span></span>

#### <a name="create-an-existing-database"></a><span data-ttu-id="df468-161">既存のデータベースを作成する</span><span class="sxs-lookup"><span data-stu-id="df468-161">Create an Existing Database</span></span>

<span data-ttu-id="df468-162">通常、既存のデータベースを対象とする場合は、既に作成されますが、このチュートリアルでは、にアクセスするデータベースを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="df468-162">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="df468-163">Visual Studio と共にインストールされるデータベースサーバーは、インストールされている Visual Studio のバージョンによって異なります。</span><span class="sxs-lookup"><span data-stu-id="df468-163">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="df468-164">Visual Studio 2010 を使用している場合は、SQL Express データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="df468-164">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="df468-165">Visual Studio 2012 を使用している場合は、 [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx)データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="df468-165">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) database.</span></span>

<span data-ttu-id="df468-166">では、データベースを生成してみましょう。</span><span class="sxs-lookup"><span data-stu-id="df468-166">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="df468-167">**ビュー- &gt; サーバーエクスプローラー**</span><span class="sxs-lookup"><span data-stu-id="df468-167">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="df468-168">[**データ接続-接続の &gt; 追加**] を右クリックします。</span><span class="sxs-lookup"><span data-stu-id="df468-168">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="df468-169">サーバーエクスプローラーからデータベースに接続していない場合は、データソースとして Microsoft SQL Server を選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="df468-169">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![データ ソースの変更](~/ef6/media/changedatasource.png)

-   <span data-ttu-id="df468-171">インストールされているものに応じて LocalDB または SQL Express に接続し、データベース名として「 **Products** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="df468-171">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **Products** as the database name</span></span>

    ![接続 LocalDB の追加](~/ef6/media/addconnectionlocaldb.png)

    ![接続 Express の追加](~/ef6/media/addconnectionexpress.png)

-   <span data-ttu-id="df468-174">[ **OK]** を選択すると、新しいデータベースを作成するかどうかを確認するメッセージが表示されます。 [**はい**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="df468-174">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![データベースの作成](~/ef6/media/createdatabase.png)

-   <span data-ttu-id="df468-176">新しいデータベースがサーバーエクスプローラーに表示され、右クリックして [**新しいクエリ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="df468-176">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="df468-177">次の SQL を新しいクエリにコピーし、クエリを右クリックして、[**実行**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="df468-177">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

``` SQL
    CREATE TABLE [dbo].[Categories] (
        [CategoryId] [int] NOT NULL IDENTITY,
        [Name] [nvarchar](max),
        CONSTRAINT [PK_dbo.Categories] PRIMARY KEY ([CategoryId])
    )

    CREATE TABLE [dbo].[Products] (
        [ProductId] [int] NOT NULL IDENTITY,
        [Name] [nvarchar](max),
        [CategoryId] [int] NOT NULL,
        CONSTRAINT [PK_dbo.Products] PRIMARY KEY ([ProductId])
    )

    CREATE INDEX [IX_CategoryId] ON [dbo].[Products]([CategoryId])

    ALTER TABLE [dbo].[Products] ADD CONSTRAINT [FK_dbo.Products_dbo.Categories_CategoryId] FOREIGN KEY ([CategoryId]) REFERENCES [dbo].[Categories] ([CategoryId]) ON DELETE CASCADE
```

#### <a name="reverse-engineer-model"></a><span data-ttu-id="df468-178">リバースエンジニアリングモデル</span><span class="sxs-lookup"><span data-stu-id="df468-178">Reverse Engineer Model</span></span>

<span data-ttu-id="df468-179">ここでは、Visual Studio の一部として含まれている Entity Framework Designer を使用して、モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="df468-179">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="df468-180">**プロジェクト- &gt; 新しい項目の追加...**</span><span class="sxs-lookup"><span data-stu-id="df468-180">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="df468-181">左側のメニューから [**データ**] を選択し、[ADO.NET] をクリックし**Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="df468-181">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="df468-182">名前として「 **Productmodel** 」と入力し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="df468-182">Enter **ProductModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="df468-183">**Entity Data Model ウィザード**が起動します。</span><span class="sxs-lookup"><span data-stu-id="df468-183">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="df468-184">[**データベースから生成**] を選択し、[**次へ**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="df468-184">Select **Generate from Database** and click **Next**</span></span>

    ![モデル コンテンツの選択](~/ef6/media/choosemodelcontents.png)

-   <span data-ttu-id="df468-186">最初のセクションで作成したデータベースへの接続を選択し、接続文字列の名前として「 **Productcontext** 」と入力して、[**次へ**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="df468-186">Select the connection to the database you created in the first section, enter **ProductContext** as the name of the connection string and click **Next**</span></span>

    ![接続の選択](~/ef6/media/chooseyourconnection.png)

-   <span data-ttu-id="df468-188">[テーブル] の横にあるチェックボックスをオンにしてすべてのテーブルをインポートし、[完了] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="df468-188">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![オブジェクトの選択](~/ef6/media/chooseyourobjects.png)

<span data-ttu-id="df468-190">リバースエンジニアリングプロセスが完了すると、新しいモデルがプロジェクトに追加され、Entity Framework Designer で表示できるように開かれます。</span><span class="sxs-lookup"><span data-stu-id="df468-190">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="df468-191">データベースの接続の詳細を含む App.config ファイルもプロジェクトに追加されています。</span><span class="sxs-lookup"><span data-stu-id="df468-191">An App.config file has also been added to your project with the connection details for the database.</span></span>

#### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="df468-192">Visual Studio 2010 の追加手順</span><span class="sxs-lookup"><span data-stu-id="df468-192">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="df468-193">Visual Studio 2010 で作業している場合は、EF6 コード生成を使用するように EF デザイナーを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="df468-193">If you are working in Visual Studio 2010 then you will need to update the EF designer to use EF6 code generation.</span></span>

-   <span data-ttu-id="df468-194">EF デザイナーでモデルの空の場所を右クリックし、[**コード生成項目の追加...** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="df468-194">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="df468-195">左側のメニューから [**オンラインテンプレート**] を選択し、 **dbcontext**を検索します。</span><span class="sxs-lookup"><span data-stu-id="df468-195">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="df468-196">**C の EF 6.X DbContext \# ジェネレーター**を選択し、名前として「製品**モデル**」と入力して、[追加] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="df468-196">Select the **EF 6.x DbContext Generator for C\#,** enter **ProductsModel** as the name and click Add</span></span>

#### <a name="updating-code-generation-for-data-binding"></a><span data-ttu-id="df468-197">データバインディングのコード生成の更新</span><span class="sxs-lookup"><span data-stu-id="df468-197">Updating code generation for data binding</span></span>

<span data-ttu-id="df468-198">EF は T4 テンプレートを使用して、モデルからコードを生成します。</span><span class="sxs-lookup"><span data-stu-id="df468-198">EF generates code from your model using T4 templates.</span></span> <span data-ttu-id="df468-199">Visual Studio に付属しているテンプレート、または Visual Studio ギャラリーからダウンロードしたテンプレートは、汎用的な使用を目的としています。</span><span class="sxs-lookup"><span data-stu-id="df468-199">The templates shipped with Visual Studio or downloaded from the Visual Studio gallery are intended for general purpose use.</span></span> <span data-ttu-id="df468-200">これは、これらのテンプレートから生成されたエンティティに単純な ICollection T プロパティがあることを意味 &lt; &gt; します。</span><span class="sxs-lookup"><span data-stu-id="df468-200">This means that the entities generated from these templates have simple ICollection&lt;T&gt; properties.</span></span> <span data-ttu-id="df468-201">ただし、WPF を使用してデータバインディングを実行する場合は、コレクションのプロパティに**system.collections.objectmodel.observablecollection**を使用することをお勧めします。これにより、wpf がコレクションに加えられた変更を追跡できるようになります。</span><span class="sxs-lookup"><span data-stu-id="df468-201">However, when doing data binding using WPF it is desirable to use **ObservableCollection** for collection properties so that WPF can keep track of changes made to the collections.</span></span> <span data-ttu-id="df468-202">この最終的には、System.collections.objectmodel.observablecollection を使用するようにテンプレートを変更します。</span><span class="sxs-lookup"><span data-stu-id="df468-202">To this end we will to modify the templates to use ObservableCollection.</span></span>

-   <span data-ttu-id="df468-203">**ソリューションエクスプローラー**を開き、 **productmodel. .edmx**ファイルを検索します。</span><span class="sxs-lookup"><span data-stu-id="df468-203">Open the **Solution Explorer** and find **ProductModel.edmx** file</span></span>
-   <span data-ttu-id="df468-204">ProductModel. .edmx ファイルの下に入れ子になっている**ProductModel.tt**ファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="df468-204">Find the **ProductModel.tt** file which will be nested under the ProductModel.edmx file</span></span>

    ![WPF 製品モデルテンプレート](~/ef6/media/wpfproductmodeltemplate.png)

-   <span data-ttu-id="df468-206">ProductModel.tt ファイルをダブルクリックして、Visual Studio エディターで開きます。</span><span class="sxs-lookup"><span data-stu-id="df468-206">Double-click on the ProductModel.tt file to open it in the Visual Studio editor</span></span>
-   <span data-ttu-id="df468-207">"**ICollection**" の2つの出現箇所を検索し、"**system.collections.objectmodel.observablecollection**" に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="df468-207">Find and replace the two occurrences of “**ICollection**” with “**ObservableCollection**”.</span></span> <span data-ttu-id="df468-208">これらは、約296行と484行にあります。</span><span class="sxs-lookup"><span data-stu-id="df468-208">These are located approximately at lines 296 and 484.</span></span>
-   <span data-ttu-id="df468-209">最初に出現する "**HashSet**" を検索し、"**system.collections.objectmodel.observablecollection**" に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="df468-209">Find and replace the first occurrence of “**HashSet**” with “**ObservableCollection**”.</span></span> <span data-ttu-id="df468-210">この現象は約50行目にあります。</span><span class="sxs-lookup"><span data-stu-id="df468-210">This occurrence is located approximately at line 50.</span></span> <span data-ttu-id="df468-211">コードの後半で見つかった HashSet の2回目の出現を置き換え**ないでください**。</span><span class="sxs-lookup"><span data-stu-id="df468-211">**Do not** replace the second occurrence of HashSet found later in the code.</span></span>
-   <span data-ttu-id="df468-212">"System.string" だけを検索し、**"system.string\*\*\*\*. objectmodel**" に置換します。</span><span class="sxs-lookup"><span data-stu-id="df468-212">Find and replace the only occurrence of “**System.Collections.Generic**” with “**System.Collections.ObjectModel**”.</span></span> <span data-ttu-id="df468-213">これは約424行目にあります。</span><span class="sxs-lookup"><span data-stu-id="df468-213">This is located approximately at line 424.</span></span>
-   <span data-ttu-id="df468-214">ProductModel.tt ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="df468-214">Save the ProductModel.tt file.</span></span> <span data-ttu-id="df468-215">これにより、エンティティのコードが再生成されます。</span><span class="sxs-lookup"><span data-stu-id="df468-215">This should cause the code for entities to be regenerated.</span></span> <span data-ttu-id="df468-216">コードが自動的に再生成されない場合は、ProductModel.tt を右クリックし、[カスタムツールの実行] を選択します。</span><span class="sxs-lookup"><span data-stu-id="df468-216">If the code does not regenerate automatically, then right click on ProductModel.tt and choose “Run Custom Tool”.</span></span>

<span data-ttu-id="df468-217">Category.cs ファイル (ProductModel.tt の下に入れ子になっています) を開くと、Products コレクションの type が\*\*System.collections.objectmodel.observablecollection &lt; Product &gt; \*\*であることがわかります。</span><span class="sxs-lookup"><span data-stu-id="df468-217">If you now open the Category.cs file (which is nested under ProductModel.tt) then you should see that the Products collection has the type **ObservableCollection&lt;Product&gt;**.</span></span>

<span data-ttu-id="df468-218">プロジェクトをコンパイルします。</span><span class="sxs-lookup"><span data-stu-id="df468-218">Compile the project.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="df468-219">遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="df468-219">Lazy Loading</span></span>

<span data-ttu-id="df468-220">**Product**クラスの**Category**クラスおよび**category**プロパティの**Products**プロパティは、ナビゲーションプロパティです。</span><span class="sxs-lookup"><span data-stu-id="df468-220">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="df468-221">Entity Framework では、ナビゲーションプロパティを使用して、2つのエンティティ型間のリレーションシップをナビゲートすることができます。</span><span class="sxs-lookup"><span data-stu-id="df468-221">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="df468-222">EF では、ナビゲーションプロパティに初めてアクセスするときに、関連エンティティをデータベースから自動的に読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="df468-222">EF gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="df468-223">この種類の読み込み (遅延読み込みと呼ばれます) を使用すると、各ナビゲーションプロパティに初めてアクセスしたときに、コンテンツがコンテキスト内に存在しない場合、データベースに対して個別のクエリが実行されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="df468-223">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="df468-224">POCO エンティティ型を使用する場合、EF は、実行時に派生プロキシ型のインスタンスを作成し、クラスの仮想プロパティをオーバーライドして読み込みフックを追加することにより、遅延読み込みを実現します。</span><span class="sxs-lookup"><span data-stu-id="df468-224">When using POCO entity types, EF achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="df468-225">関連オブジェクトの遅延読み込みを行うには、ナビゲーションプロパティ getter を**public**および**virtual** (Visual Basic で**オーバーライド**可能) として宣言し、クラスを**sealed** (Visual Basic では**NotOverridable** ) にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="df468-225">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and you class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="df468-226">Database First を使用すると、遅延読み込みを有効にするために、ナビゲーションプロパティが自動的に仮想化されます。</span><span class="sxs-lookup"><span data-stu-id="df468-226">When using Database First navigation properties are automatically made virtual to enable lazy loading.</span></span> <span data-ttu-id="df468-227">Code First セクションでは、ナビゲーションプロパティを同じ理由で仮想にすることを選択しました。</span><span class="sxs-lookup"><span data-stu-id="df468-227">In the Code First section we chose to make the navigation properties virtual for the same reason</span></span>

## <a name="bind-object-to-controls"></a><span data-ttu-id="df468-228">コントロールへのオブジェクトのバインド</span><span class="sxs-lookup"><span data-stu-id="df468-228">Bind Object to Controls</span></span>

<span data-ttu-id="df468-229">モデルで定義されているクラスを、この WPF アプリケーションのデータソースとして追加します。</span><span class="sxs-lookup"><span data-stu-id="df468-229">Add the classes that are defined in the model as data sources for this WPF application.</span></span>

-   <span data-ttu-id="df468-230">ソリューションエクスプローラーで Mainwindow.xaml をダブルクリックしてメインフォームを開き**ます。**</span><span class="sxs-lookup"><span data-stu-id="df468-230">Double-click **MainWindow.xaml** in Solution Explorer to open the main form</span></span>
-   <span data-ttu-id="df468-231">メインメニューから、[**プロジェクト- &gt; 新しいデータソースの追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="df468-231">From the main menu, select **Project -&gt; Add New Data Source …**</span></span>
    <span data-ttu-id="df468-232">(Visual Studio 2010 では、[**データ- &gt; 新しいデータソースの追加**] を選択する必要があります)。</span><span class="sxs-lookup"><span data-stu-id="df468-232">(in Visual Studio 2010, you need to select **Data -&gt; Add New Data Source…**)</span></span>
-   <span data-ttu-id="df468-233">[データソースの選択] ウィンドウで、[**オブジェクト**] を選択し、[**次へ**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="df468-233">In the Choose a Data Source Typewindow, select **Object** and click **Next**</span></span>
-   <span data-ttu-id="df468-234">[データオブジェクトの選択] ダイアログボックスで、 **WPFwithEFSample**を2回展開し、   [**カテゴリ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="df468-234">In the Select the Data Objects dialog, unfold the **WPFwithEFSample** two times and select **Category**</span></span>  
    <span data-ttu-id="df468-235">\***Product**データソースを選択する必要はありません。これは、**カテゴリ**データソースで**製品**のプロパティを使用して取得するからです。\*</span><span class="sxs-lookup"><span data-stu-id="df468-235">*There is no need to select the **Product** data source, because we will get to it through the **Product**’s property on the **Category** data source*</span></span>  

    ![データオブジェクトの選択](~/ef6/media/selectdataobjects.png)

-   <span data-ttu-id="df468-237">**[完了]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="df468-237">Click **Finish.**</span></span>
-   <span data-ttu-id="df468-238">[データソース] ウィンドウが表示されていない場合は、Mainwindow.xaml ウィンドウの横に開かれます。 [データソース] ウィンドウが表示されて*いない場合は、[表示]、[ \*\* &gt; その他のウィンドウ- &gt; データソース\*\*] を選択し*ます。</span><span class="sxs-lookup"><span data-stu-id="df468-238">The Data Sources window is opened next to the MainWindow.xaml window *If the Data Sources window is not showing up, select **View -&gt; Other Windows-&gt; Data Sources***</span></span>
-   <span data-ttu-id="df468-239">[データソース] ウィンドウが自動的に非表示にならないように、ピンアイコンを押します。</span><span class="sxs-lookup"><span data-stu-id="df468-239">Press the pin icon, so the Data Sources window does not auto hide.</span></span> <span data-ttu-id="df468-240">ウィンドウが既に表示されている場合は、[更新] ボタンをクリックしなければならないことがあります。</span><span class="sxs-lookup"><span data-stu-id="df468-240">You may need to hit the refresh button if the window was already visible.</span></span>

    ![Data Sources](~/ef6/media/datasources.png)

-   <span data-ttu-id="df468-242"> **カテゴリ**データソースを選択し、フォーム上にドラッグします。</span><span class="sxs-lookup"><span data-stu-id="df468-242">Select the **Category** data source and drag it on the form.</span></span>

<span data-ttu-id="df468-243">このソースをドラッグすると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="df468-243">The following happened when we dragged this source:</span></span>

-   <span data-ttu-id="df468-244">**カテゴリ Viewsource**リソースと**カテゴリ DATAGRID**コントロールが XAML に追加されました</span><span class="sxs-lookup"><span data-stu-id="df468-244">The **categoryViewSource** resource and the **categoryDataGrid** control were added to XAML</span></span> 
-   <span data-ttu-id="df468-245">親 Grid 要素の DataContext プロパティが "{StaticResource**カテゴリ Viewsource** }" に設定されました。</span><span class="sxs-lookup"><span data-stu-id="df468-245">The DataContext property on the parent Grid element was set to "{StaticResource **categoryViewSource** }".</span></span><span data-ttu-id="df468-246">**カテゴリ Viewsource**リソースは、外側の親 Grid 要素のバインドソースとして機能し \\ ます。</span><span class="sxs-lookup"><span data-stu-id="df468-246"> The **categoryViewSource** resource serves as a binding source for the outer\\parent Grid element.</span></span> <span data-ttu-id="df468-247">その後、内側の Grid 要素は、親グリッドから DataContext 値を継承します (カテゴリの Datagrid の ItemsSource プロパティは "{Binding}" に設定されています)。</span><span class="sxs-lookup"><span data-stu-id="df468-247">The inner Grid elements then inherit the DataContext value from the parent Grid (the categoryDataGrid’s ItemsSource property is set to "{Binding}")</span></span>

``` xml
    <Window.Resources>
        <CollectionViewSource x:Key="categoryViewSource"
                                d:DesignSource="{d:DesignInstance {x:Type local:Category}, CreateList=True}"/>
    </Window.Resources>
    <Grid DataContext="{StaticResource categoryViewSource}">
        <DataGrid x:Name="categoryDataGrid" AutoGenerateColumns="False" EnableRowVirtualization="True"
                    ItemsSource="{Binding}" Margin="13,13,43,191"
                    RowDetailsVisibilityMode="VisibleWhenSelected">
            <DataGrid.Columns>
                <DataGridTextColumn x:Name="categoryIdColumn" Binding="{Binding CategoryId}"
                                    Header="Category Id" Width="SizeToHeader"/>
                <DataGridTextColumn x:Name="nameColumn" Binding="{Binding Name}"
                                    Header="Name" Width="SizeToHeader"/>
            </DataGrid.Columns>
        </DataGrid>
    </Grid>
```

## <a name="adding-a-details-grid"></a><span data-ttu-id="df468-248">詳細グリッドの追加</span><span class="sxs-lookup"><span data-stu-id="df468-248">Adding a Details Grid</span></span>

<span data-ttu-id="df468-249">カテゴリを表示するグリッドが用意できたので、[詳細] グリッドを追加して、関連付けられている製品を表示します。</span><span class="sxs-lookup"><span data-stu-id="df468-249">Now that we have a grid to display Categories let's add a details grid to display the associated Products.</span></span>

-   <span data-ttu-id="df468-250"> **Category**データソースの [ **Products** ] プロパティを選択し、フォームにドラッグします。</span><span class="sxs-lookup"><span data-stu-id="df468-250">Select the **Products** property from under the **Category** data source and drag it on the form.</span></span>
    -   <span data-ttu-id="df468-251">**カテゴリ Productviewsource**リソースと**PRODUCTDATAGRID** grid が XAML に追加されます。</span><span class="sxs-lookup"><span data-stu-id="df468-251">The **categoryProductsViewSource** resource and **productDataGrid** grid are added to XAML</span></span>
    -   <span data-ttu-id="df468-252">このリソースのバインドパスは Products に設定されています</span><span class="sxs-lookup"><span data-stu-id="df468-252">The binding path for this resource is set to Products</span></span>
    -   <span data-ttu-id="df468-253">WPF データバインディングフレームワークにより、選択したカテゴリに関連する製品のみが**Productdatagrid**に表示されるようになります。</span><span class="sxs-lookup"><span data-stu-id="df468-253">WPF data-binding framework ensures that only Products related to the selected Category show up in **productDataGrid**</span></span>
-   <span data-ttu-id="df468-254">ツールボックスから、**ボタン**をフォームにドラッグします。</span><span class="sxs-lookup"><span data-stu-id="df468-254">From the Toolbox, drag **Button** on to the form.</span></span> <span data-ttu-id="df468-255">**Name**プロパティを**buttonsave**に設定し、 **Content**プロパティを**save**に設定します。</span><span class="sxs-lookup"><span data-stu-id="df468-255">Set the **Name** property to **buttonSave** and the **Content** property to **Save**.</span></span>

<span data-ttu-id="df468-256">フォームは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="df468-256">The form should look similar to this:</span></span>

![Designer](~/ef6/media/designer.png) 

## <a name="add-code-that-handles-data-interaction"></a><span data-ttu-id="df468-258">データの相互作用を処理するコードを追加する</span><span class="sxs-lookup"><span data-stu-id="df468-258">Add Code that Handles Data Interaction</span></span>

<span data-ttu-id="df468-259">ここでは、いくつかのイベントハンドラーをメインウィンドウに追加します。</span><span class="sxs-lookup"><span data-stu-id="df468-259">It's time to add some event handlers to the main window.</span></span>

-   <span data-ttu-id="df468-260">XAML ウィンドウで\*\* &lt; ウィンドウ\*\*要素をクリックすると、メインウィンドウが選択されます。</span><span class="sxs-lookup"><span data-stu-id="df468-260">In the XAML window, click on the **&lt;Window** element, this selects the main window</span></span>
-   <span data-ttu-id="df468-261">[**プロパティ**] ウィンドウの右上にある [**イベント**] をクリックし、**読み込ま**れたラベルの右にあるテキストボックスをダブルクリックします。</span><span class="sxs-lookup"><span data-stu-id="df468-261">In the **Properties** window choose **Events** at the top right, then double-click the text box to right of the **Loaded** label</span></span>

    ![メインウィンドウのプロパティ](~/ef6/media/mainwindowproperties.png)

-   <span data-ttu-id="df468-263">また、デザイナーの [保存] ボタンをダブルクリックして、[**保存**] ボタンの**click**イベントを追加します。</span><span class="sxs-lookup"><span data-stu-id="df468-263">Also add the **Click** event for the **Save** button by double-clicking the Save button in the designer.</span></span> 

<span data-ttu-id="df468-264">これにより、フォームの分離コードが表示されます。 ProductContext を使用してデータアクセスを実行するようにコードを編集します。</span><span class="sxs-lookup"><span data-stu-id="df468-264">This brings you to the code behind for the form, we'll now edit the code to use the ProductContext to perform data access.</span></span> <span data-ttu-id="df468-265">次に示すように、Mainwindow.xaml のコードを更新します。</span><span class="sxs-lookup"><span data-stu-id="df468-265">Update the code for the MainWindow as shown below.</span></span>

<span data-ttu-id="df468-266">このコードは、 **Productcontext**の実行時間の長いインスタンスを宣言します。</span><span class="sxs-lookup"><span data-stu-id="df468-266">The code declares a long-running instance of **ProductContext**.</span></span> <span data-ttu-id="df468-267">**Productcontext**オブジェクトは、データをクエリしてデータベースに保存するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="df468-267">The **ProductContext** object is used to query and save data to the database.</span></span> <span data-ttu-id="df468-268">その後、 **Productcontext**インスタンスの**Dispose ()** が、オーバーライドされた**onclosing**メソッドから呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="df468-268">The **Dispose()** on the **ProductContext** instance is then called from the overridden **OnClosing** method.</span></span><span data-ttu-id="df468-269">コードのコメントには、コードの動作についての詳細が記載されています。</span><span class="sxs-lookup"><span data-stu-id="df468-269"> The code comments provide details about what the code does.</span></span>

``` csharp
    using System.Data.Entity;
    using System.Linq;
    using System.Windows;

    namespace WPFwithEFSample
    {
        public partial class MainWindow : Window
        {
            private ProductContext _context = new ProductContext();
            public MainWindow()
            {
                InitializeComponent();
            }

            private void Window_Loaded(object sender, RoutedEventArgs e)
            {
                System.Windows.Data.CollectionViewSource categoryViewSource =
                    ((System.Windows.Data.CollectionViewSource)(this.FindResource("categoryViewSource")));

                // Load is an extension method on IQueryable,
                // defined in the System.Data.Entity namespace.
                // This method enumerates the results of the query,
                // similar to ToList but without creating a list.
                // When used with Linq to Entities this method
                // creates entity objects and adds them to the context.
                _context.Categories.Load();

                // After the data is loaded call the DbSet<T>.Local property
                // to use the DbSet<T> as a binding source.
                categoryViewSource.Source = _context.Categories.Local;
            }

            private void buttonSave_Click(object sender, RoutedEventArgs e)
            {
                // When you delete an object from the related entities collection
                // (in this case Products), the Entity Framework doesn’t mark
                // these child entities as deleted.
                // Instead, it removes the relationship between the parent and the child
                // by setting the parent reference to null.
                // So we manually have to delete the products
                // that have a Category reference set to null.

                // The following code uses LINQ to Objects
                // against the Local collection of Products.
                // The ToList call is required because otherwise the collection will be modified
                // by the Remove call while it is being enumerated.
                // In most other situations you can use LINQ to Objects directly
                // against the Local property without using ToList first.
                foreach (var product in _context.Products.Local.ToList())
                {
                    if (product.Category == null)
                    {
                        _context.Products.Remove(product);
                    }
                }

                _context.SaveChanges();
                // Refresh the grids so the database generated values show up.
                this.categoryDataGrid.Items.Refresh();
                this.productsDataGrid.Items.Refresh();
            }

            protected override void OnClosing(System.ComponentModel.CancelEventArgs e)
            {
                base.OnClosing(e);
                this._context.Dispose();
            }
        }

    }
```

## <a name="test-the-wpf-application"></a><span data-ttu-id="df468-270">WPF アプリケーションのテスト</span><span class="sxs-lookup"><span data-stu-id="df468-270">Test the WPF Application</span></span>

-   <span data-ttu-id="df468-271">アプリケーションをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="df468-271">Compile and run the application.</span></span> <span data-ttu-id="df468-272">Code First を使用した場合は、 **WPFwithEFSample**データベースが作成されていることがわかります。</span><span class="sxs-lookup"><span data-stu-id="df468-272">If you used Code First, then you will see that a **WPFwithEFSample.ProductContext** database is created for you.</span></span>
-   <span data-ttu-id="df468-273">プライマリキーがデータベースによって生成されるため、上部のグリッドにカテゴリ名を入力し、下部のグリッドに製品名を入力して*も、ID 列には何も入力されません*。</span><span class="sxs-lookup"><span data-stu-id="df468-273">Enter a category name in the top grid and product names in the bottom grid *Do not enter anything in ID columns, because the primary key is generated by the database*</span></span>

    ![新しいカテゴリと製品を含むメインウィンドウ](~/ef6/media/screen1.png)

-   <span data-ttu-id="df468-275">[**保存**] ボタンをクリックして、データをデータベースに保存します。</span><span class="sxs-lookup"><span data-stu-id="df468-275">Press the **Save** button to save the data to the database</span></span>

<span data-ttu-id="df468-276">DbContext の**SaveChanges ()** を呼び出した後、id にはデータベースで生成された値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="df468-276">After the call to DbContext’s **SaveChanges()**, the IDs are populated with the database generated values.</span></span> <span data-ttu-id="df468-277">**SaveChanges ()** 後に**Refresh ()** を呼び出したため、 **DataGrid**コントロールも新しい値で更新されます。</span><span class="sxs-lookup"><span data-stu-id="df468-277">Because we called **Refresh()** after **SaveChanges()** the **DataGrid** controls are updated with the new values as well.</span></span>

![Id が設定されたメインウィンドウ](~/ef6/media/screen2.png)

## <a name="additional-resources"></a><span data-ttu-id="df468-279">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="df468-279">Additional Resources</span></span>

<span data-ttu-id="df468-280">WPF を使用したコレクションへのデータバインディングの詳細については、WPF のドキュメントの[このトピック](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="df468-280">To learn more about data binding to collections using WPF, see [this topic](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections) in the WPF documentation.</span></span>  
