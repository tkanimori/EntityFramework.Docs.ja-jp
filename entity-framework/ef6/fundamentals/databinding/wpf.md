---
title: WPF を使用したデータバインド - EF6
author: divega
ms.date: 04/02/2020
ms.assetid: e90d48e6-bea7785-47ef-b756-7b89cce4daf0
ms.openlocfilehash: 6908e2a7597d0c199620c6015ed3ea06226c5ea9
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2020
ms.locfileid: "80639140"
---
> [!IMPORTANT]
> <span data-ttu-id="23a3e-102">**このドキュメントは、.NET Framework の WPF でのみ有効です。**</span><span class="sxs-lookup"><span data-stu-id="23a3e-102">**This document is valid for WPF on the .NET Framework only**</span></span>
>
> <span data-ttu-id="23a3e-103">このドキュメントでは、.NET Framework での WPF のデータバインディングについて説明します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-103">This document describes databinding for WPF on the .NET Framework.</span></span> <span data-ttu-id="23a3e-104">新しい .NET Core プロジェクトの場合は、エンティティ フレームワーク 6 ではなく[EF Core](/ef/core)を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="23a3e-104">For new .NET Core projects, we recommend you use [EF Core](/ef/core) instead of Entity Framework 6.</span></span> <span data-ttu-id="23a3e-105">EF Core でのデータバインディングのドキュメントは、[問題#778](https://github.com/dotnet/EntityFramework.Docs/issues/778)で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="23a3e-105">The documentation for databinding in EF Core is tracked in [Issue #778](https://github.com/dotnet/EntityFramework.Docs/issues/778).</span></span>

# <a name="databinding-with-wpf"></a><span data-ttu-id="23a3e-106">WPF とのデータバインド</span><span class="sxs-lookup"><span data-stu-id="23a3e-106">Databinding with WPF</span></span>
<span data-ttu-id="23a3e-107">この手順のチュートリアルでは、"マスター詳細" フォームで POCO 型を WPF コントロールにバインドする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-107">This step-by-step walkthrough shows how to bind POCO types to WPF controls in a “master-detail" form.</span></span> <span data-ttu-id="23a3e-108">アプリケーションは、Entity Framework API を使用して、データベースからのデータをオブジェクトに格納し、変更を追跡し、データベースにデータを永続化します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-108">The application uses the Entity Framework APIs to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="23a3e-109">モデルは、**一**対多の関係に参加する 2 つのタイプを定義\\します: カテゴリ (\\主体マスター) と**製品**(従属詳細)。</span><span class="sxs-lookup"><span data-stu-id="23a3e-109">The model defines two types that participate in one-to-many relationship: **Category** (principal\\master) and **Product** (dependent\\detail).</span></span> <span data-ttu-id="23a3e-110">次に、Visual Studio ツールを使用して、モデルで定義されている型を WPF コントロールにバインドします。</span><span class="sxs-lookup"><span data-stu-id="23a3e-110">Then, the Visual Studio tools are used to bind the types defined in the model to the WPF controls.</span></span> <span data-ttu-id="23a3e-111">WPF データ バインディング フレームワークを使用すると、関連するオブジェクト間のナビゲーションが可能になり、マスター ビューで行を選択すると、詳細ビューが対応する子データで更新されます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-111">The WPF data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="23a3e-112">このチュートリアルのスクリーン ショットとコード一覧は、Visual Studio 2013 から取得されますが、このチュートリアルは Visual Studio 2012 または Visual Studio 2010 で完了できます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-112">The screen shots and code listings in this walkthrough are taken from Visual Studio 2013 but you can complete this walkthrough with Visual Studio 2012 or Visual Studio 2010.</span></span>

## <a name="use-the-object-option-for-creating-wpf-data-sources"></a><span data-ttu-id="23a3e-113">WPF データ ソースを作成するための 'オブジェクト' オプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-113">Use the 'Object' Option for Creating WPF Data Sources</span></span>

<span data-ttu-id="23a3e-114">以前のバージョンの Entity Framework では、EF デザイナーで作成されたモデルに基づいて新しいデータ ソースを作成するときに **、データベース**オプションを使用することをお勧めしていました。</span><span class="sxs-lookup"><span data-stu-id="23a3e-114">With previous version of Entity Framework we used to recommend using the **Database** option when creating a new Data Source based on a model created with the EF Designer.</span></span> <span data-ttu-id="23a3e-115">これは、デザイナーが、ObjectContext から派生したコンテキストと、EntityObject から派生したエンティティ クラスを生成するためです。</span><span class="sxs-lookup"><span data-stu-id="23a3e-115">This was because the designer would generate a context that derived from ObjectContext and entity classes that derived from EntityObject.</span></span> <span data-ttu-id="23a3e-116">データベース オプションを使用すると、この API サーフェスと対話するための最適なコードを記述できます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-116">Using the Database option would help you write the best code for interacting with this API surface.</span></span>

<span data-ttu-id="23a3e-117">2012 年と Visual Studio 2013 の EF デザイナーは、単純な POCO エンティティ クラスと共に DbContext から派生したコンテキストを生成します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-117">The EF Designers for Visual Studio 2012 and Visual Studio 2013 generate a context that derives from DbContext together with simple POCO entity classes.</span></span> <span data-ttu-id="23a3e-118">Visual Studio 2010 では、このチュートリアルで後述するように、DbContext を使用するコード生成テンプレートにスワップすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="23a3e-118">With Visual Studio 2010 we recommend swapping to a code generation template that uses DbContext as described later in this walkthrough.</span></span>

<span data-ttu-id="23a3e-119">DbContext API サーフェスを使用する場合は、このチュートリアルに示すように、新しいデータ ソースを作成するときに**オブジェクト**オプションを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23a3e-119">When using the DbContext API surface you should use the **Object** option when creating a new Data Source, as shown in this walkthrough.</span></span>

<span data-ttu-id="23a3e-120">必要に応じて、EF デザイナーで作成されたモデルの[ObjectContext ベースのコード生成に戻](~/ef6/modeling/designer/codegen/legacy-objectcontext.md)すことができます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-120">If needed, you can [revert to ObjectContext based code generation](~/ef6/modeling/designer/codegen/legacy-objectcontext.md) for models created with the EF Designer.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="23a3e-121">前提条件</span><span class="sxs-lookup"><span data-stu-id="23a3e-121">Pre-Requisites</span></span>

<span data-ttu-id="23a3e-122">このチュートリアルを完了するには、Visual Studio 2013、Visual Studio 2012 または Visual Studio 2010 がインストールされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="23a3e-122">You need to have Visual Studio 2013, Visual Studio 2012 or Visual Studio 2010 installed to complete this walkthrough.</span></span>

<span data-ttu-id="23a3e-123">Visual Studio 2010 を使用している場合は、NuGet をインストールする必要もあります。</span><span class="sxs-lookup"><span data-stu-id="23a3e-123">If you are using Visual Studio 2010, you also have to install NuGet.</span></span> <span data-ttu-id="23a3e-124">詳細については、「 [NuGet](https://docs.microsoft.com/nuget/install-nuget-client-tools)のインストール 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23a3e-124">For more information, see [Installing NuGet](https://docs.microsoft.com/nuget/install-nuget-client-tools).</span></span>  

## <a name="create-the-application"></a><span data-ttu-id="23a3e-125">アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="23a3e-125">Create the Application</span></span>

-   <span data-ttu-id="23a3e-126">Visual Studio を開きます</span><span class="sxs-lookup"><span data-stu-id="23a3e-126">Open Visual Studio</span></span>
-   <span data-ttu-id="23a3e-127">**ファイル&gt;-&gt;新規 - プロジェクト.**</span><span class="sxs-lookup"><span data-stu-id="23a3e-127">**File -&gt; New -&gt; Project….**</span></span>
-   <span data-ttu-id="23a3e-128">左側のペインで **[ウィンドウ** ] を選択し、右側のペインで **[WPF アプリケーション**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-128">Select **Windows** in the left pane and **WPFApplication** in the right pane</span></span>
-   <span data-ttu-id="23a3e-129"> 名前 **として入力します。**</span><span class="sxs-lookup"><span data-stu-id="23a3e-129">Enter **WPFwithEFSample** as the name</span></span>
-   <span data-ttu-id="23a3e-130"> **OK を選択します。**</span><span class="sxs-lookup"><span data-stu-id="23a3e-130">Select **OK**</span></span>

## <a name="install-the-entity-framework-nuget-package"></a><span data-ttu-id="23a3e-131">エンティティ フレームワーク NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="23a3e-131">Install the Entity Framework NuGet package</span></span>

-   <span data-ttu-id="23a3e-132">ソリューション エクスプローラーで **、WinFormswithEFSample**プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="23a3e-132">In Solution Explorer, right-click on the **WinFormswithEFSample** project</span></span>
-   <span data-ttu-id="23a3e-133">**[NuGet パッケージの管理] を選択します。**</span><span class="sxs-lookup"><span data-stu-id="23a3e-133">Select **Manage NuGet Packages…**</span></span>
-   <span data-ttu-id="23a3e-134">[NuGet パッケージの管理] ダイアログで、[**オンライン**] タブを選択し **、EntityFramework**パッケージを選択します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-134">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
-   <span data-ttu-id="23a3e-135">[インストール] をクリック**します。**</span><span class="sxs-lookup"><span data-stu-id="23a3e-135">Click **Install**</span></span>  
    >[!NOTE]
    > <span data-ttu-id="23a3e-136">エンティティフレームワーク アセンブリに加えて、システム.コンポーネントモデルへの参照も追加されます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-136">In addition to the EntityFramework assembly a reference to System.ComponentModel.DataAnnotations is also added.</span></span> <span data-ttu-id="23a3e-137">プロジェクトに System.Data.Entity への参照がある場合、EntityFramework パッケージのインストール時に削除されます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-137">If the project has a reference to System.Data.Entity, then it will be removed when the EntityFramework package is installed.</span></span> <span data-ttu-id="23a3e-138">エンティティ フレームワーク 6 アプリケーションでは、System.Data.Entity アセンブリは使用されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="23a3e-138">The System.Data.Entity assembly is no longer used for Entity Framework 6 applications.</span></span>

## <a name="define-a-model"></a><span data-ttu-id="23a3e-139">モデルの定義</span><span class="sxs-lookup"><span data-stu-id="23a3e-139">Define a Model</span></span>

<span data-ttu-id="23a3e-140">このチュートリアルでは、コード ファーストまたは EF デザイナーを使用してモデルを実装できます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-140">In this walkthrough you can chose to implement a model using Code First or the EF Designer.</span></span> <span data-ttu-id="23a3e-141">次の 2 つのセクションのいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-141">Complete one of the two following sections.</span></span>

### <a name="option-1-define-a-model-using-code-first"></a><span data-ttu-id="23a3e-142">方法 1: コードを使用してモデルを定義する</span><span class="sxs-lookup"><span data-stu-id="23a3e-142">Option 1: Define a Model using Code First</span></span>

<span data-ttu-id="23a3e-143">このセクションでは、Code First を使用してモデルと関連付けられたデータベースを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-143">This section shows how to create a model and its associated database using Code First.</span></span> <span data-ttu-id="23a3e-144">データベース ファーストを使用して EF デザイナを使用してデータベースからモデルをリバース エンジニアリングする場合は、次のセクションに進んでください (「**オプション 2: データベースファーストを使用してモデルを定義する」)。**</span><span class="sxs-lookup"><span data-stu-id="23a3e-144">Skip to the next section (**Option 2: Define a model using Database First)** if you would rather use Database First to reverse engineer your model from a database using the EF designer</span></span>

<span data-ttu-id="23a3e-145">コードファースト開発を使用する場合は、概念 (ドメイン) モデルを定義する .NET Framework クラスを記述します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-145">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span>

-   <span data-ttu-id="23a3e-146">新しいクラスを **WPFwithEF サンプルに追加します。**</span><span class="sxs-lookup"><span data-stu-id="23a3e-146">Add a new class to the **WPFwithEFSample:**</span></span>
    -   <span data-ttu-id="23a3e-147">プロジェクト名を右クリックします。</span><span class="sxs-lookup"><span data-stu-id="23a3e-147">Right-click on the project name</span></span>
    -   <span data-ttu-id="23a3e-148">[**追加**] を選択し、[**新しいアイテム] を選択します。**</span><span class="sxs-lookup"><span data-stu-id="23a3e-148">Select **Add**, then **New Item**</span></span>
    -   <span data-ttu-id="23a3e-149">**[クラス] を**選択し、クラス名に **製品** を入力します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-149">Select **Class** and enter **Product** for the class name</span></span>
-   <span data-ttu-id="23a3e-150"> **Product** クラス定義を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-150">Replace the **Product** class definition with the following code:</span></span>

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

<span data-ttu-id="23a3e-151">**Product**クラスの**カテゴリ**クラスと**カテゴリ**プロパティの**Products**プロパティは、ナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="23a3e-151">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="23a3e-152">Entity Framework では、ナビゲーション プロパティは、2 つのエンティティ型間のリレーションシップをナビゲートする方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-152">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="23a3e-153">エンティティの定義に加えて、DbContext から派生し、DbSet&lt;TEntity プロパティを公開するクラスを&gt;定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23a3e-153">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="23a3e-154">DbSet&lt;TEntity&gt;プロパティは、モデルに含める型をコンテキストに知らせます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-154">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="23a3e-155">DbContext 派生型のインスタンスは、実行時にエンティティ オブジェクトを管理します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-155">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

-   <span data-ttu-id="23a3e-156">次の定義を使用して、新しい**ProductContext**クラスをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-156">Add a new **ProductContext** class to the project with the following definition:</span></span>

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

<span data-ttu-id="23a3e-157">プロジェクトをコンパイルします。</span><span class="sxs-lookup"><span data-stu-id="23a3e-157">Compile the project.</span></span>

### <a name="option-2-define-a-model-using-database-first"></a><span data-ttu-id="23a3e-158">オプション 2: データベースを使用してモデルを定義する</span><span class="sxs-lookup"><span data-stu-id="23a3e-158">Option 2: Define a model using Database First</span></span>

<span data-ttu-id="23a3e-159">このセクションでは、データベース ファーストを使用して、EF デザイナを使用してデータベースからモデルをリバース エンジニアリングする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-159">This section shows how to use Database First to reverse engineer your model from a database using the EF designer.</span></span> <span data-ttu-id="23a3e-160">前のセクション (オプション**1: Code First を使用してモデルを定義する)** を完了した場合は、このセクションをスキップして **、遅延読み込み**セクションに進みます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-160">If you completed the previous section (**Option 1: Define a model using Code First)**, then skip this section and go straight to the **Lazy Loading** section.</span></span>

#### <a name="create-an-existing-database"></a><span data-ttu-id="23a3e-161">既存のデータベースを作成する</span><span class="sxs-lookup"><span data-stu-id="23a3e-161">Create an Existing Database</span></span>

<span data-ttu-id="23a3e-162">通常、既存のデータベースをターゲットにしている場合は既に作成されていますが、このチュートリアルでは、アクセスするデータベースを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23a3e-162">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="23a3e-163">Visual Studio と共にインストールされるデータベース サーバーは、インストールされている Visual Studio のバージョンによって異なります。</span><span class="sxs-lookup"><span data-stu-id="23a3e-163">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="23a3e-164">Visual Studio 2010 を使用している場合は、SQL エクスプレス データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-164">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="23a3e-165">Visual Studio 2012 を使用している場合は[、LocalDB](https://msdn.microsoft.com/library/hh510202.aspx)データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-165">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) database.</span></span>

<span data-ttu-id="23a3e-166">それでは、データベースを生成してみましょう。</span><span class="sxs-lookup"><span data-stu-id="23a3e-166">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="23a3e-167">**表示&gt;- サーバー エクスプローラ**</span><span class="sxs-lookup"><span data-stu-id="23a3e-167">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="23a3e-168">**[データ接続&gt;- 接続の追加]を右クリックします。**</span><span class="sxs-lookup"><span data-stu-id="23a3e-168">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="23a3e-169">サーバー エクスプローラーからデータベースに接続していない場合は、データ ソースとして SQL Server を選択する必要がある場合</span><span class="sxs-lookup"><span data-stu-id="23a3e-169">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![データ ソースの変更](~/ef6/media/changedatasource.png)

-   <span data-ttu-id="23a3e-171">インストールしたローカルまたは SQL Express に接続し、データベース名として **「製品」** と入力します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-171">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **Products** as the database name</span></span>

    ![接続ローカルデータベースの追加](~/ef6/media/addconnectionlocaldb.png)

    ![接続の追加 Express](~/ef6/media/addconnectionexpress.png)

-   <span data-ttu-id="23a3e-174">**[OK] を**選択すると、新しいデータベースを作成するかどうかの確認が求**められます。**</span><span class="sxs-lookup"><span data-stu-id="23a3e-174">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![データベースの作成](~/ef6/media/createdatabase.png)

-   <span data-ttu-id="23a3e-176">新しいデータベースがサーバー エクスプローラに表示され、右クリックして **[新しいクエリ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-176">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="23a3e-177">次の SQL を新しいクエリにコピーし、クエリを右クリックして[**実行**]を選択します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-177">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

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

#### <a name="reverse-engineer-model"></a><span data-ttu-id="23a3e-178">リバース エンジニアリング モデル</span><span class="sxs-lookup"><span data-stu-id="23a3e-178">Reverse Engineer Model</span></span>

<span data-ttu-id="23a3e-179">このモデルを作成するために、Visual Studio の一部として含まれているエンティティ フレームワーク デザイナーを使用します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-179">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="23a3e-180">**プロジェクト&gt;- 新しい項目の追加.**</span><span class="sxs-lookup"><span data-stu-id="23a3e-180">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="23a3e-181">左側のメニューから **[データ**] を選択し、**エンティティ データ モデルADO.NET**</span><span class="sxs-lookup"><span data-stu-id="23a3e-181">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="23a3e-182">**名前として製品モデル**を入力し **、[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="23a3e-182">Enter **ProductModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="23a3e-183">エンティティ データ**モデル ウィザードが起動します。**</span><span class="sxs-lookup"><span data-stu-id="23a3e-183">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="23a3e-184">[**データベースから生成]を**選択し、[**次へ**]をクリックします。</span><span class="sxs-lookup"><span data-stu-id="23a3e-184">Select **Generate from Database** and click **Next**</span></span>

    ![モデル コンテンツの選択](~/ef6/media/choosemodelcontents.png)

-   <span data-ttu-id="23a3e-186">最初のセクションで作成したデータベースへの接続を選択し、接続文字列の名前として**ProductContext**と入力し、[**次へ**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="23a3e-186">Select the connection to the database you created in the first section, enter **ProductContext** as the name of the connection string and click **Next**</span></span>

    ![接続を選択する](~/ef6/media/chooseyourconnection.png)

-   <span data-ttu-id="23a3e-188">すべてのテーブルをインポートするには、[テーブル] の横にあるチェックボックスをクリックし、[完了] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="23a3e-188">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![オブジェクトを選択する](~/ef6/media/chooseyourobjects.png)

<span data-ttu-id="23a3e-190">リバース エンジニアリング プロセスが完了すると、新しいモデルがプロジェクトに追加され、エンティティ フレームワーク デザイナーで表示できます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-190">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="23a3e-191">App.config ファイルも、データベースの接続の詳細を含むプロジェクトに追加されました。</span><span class="sxs-lookup"><span data-stu-id="23a3e-191">An App.config file has also been added to your project with the connection details for the database.</span></span>

#### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="23a3e-192">Visual Studio 2010 のその他の手順</span><span class="sxs-lookup"><span data-stu-id="23a3e-192">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="23a3e-193">Visual Studio 2010 で作業している場合は、EF6 コード生成を使用するように EF デザイナーを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23a3e-193">If you are working in Visual Studio 2010 then you will need to update the EF designer to use EF6 code generation.</span></span>

-   <span data-ttu-id="23a3e-194">EF デザイナーでモデルの空の場所を右クリックし、[**コード生成項目の追加]を選択します。**</span><span class="sxs-lookup"><span data-stu-id="23a3e-194">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="23a3e-195">左側**のメニューからオンラインテンプレート**を選択し **、DbContext**を検索します</span><span class="sxs-lookup"><span data-stu-id="23a3e-195">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="23a3e-196">**\#C 用 EF 6.x DbContext ジェネレーターを選択し、** 名前として**製品モデル**を入力し、追加 をクリックします。</span><span class="sxs-lookup"><span data-stu-id="23a3e-196">Select the **EF 6.x DbContext Generator for C\#,** enter **ProductsModel** as the name and click Add</span></span>

#### <a name="updating-code-generation-for-data-binding"></a><span data-ttu-id="23a3e-197">データ バインディング用のコード生成の更新</span><span class="sxs-lookup"><span data-stu-id="23a3e-197">Updating code generation for data binding</span></span>

<span data-ttu-id="23a3e-198">EF は T4 テンプレートを使用してモデルからコードを生成します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-198">EF generates code from your model using T4 templates.</span></span> <span data-ttu-id="23a3e-199">Visual Studio に付属のテンプレート、または Visual Studio ギャラリーからダウンロードされたテンプレートは、汎用的な用途を目的としています。</span><span class="sxs-lookup"><span data-stu-id="23a3e-199">The templates shipped with Visual Studio or downloaded from the Visual Studio gallery are intended for general purpose use.</span></span> <span data-ttu-id="23a3e-200">つまり、これらのテンプレートから生成されたエンティティには、単純な&lt;ICollection T&gt;プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="23a3e-200">This means that the entities generated from these templates have simple ICollection&lt;T&gt; properties.</span></span> <span data-ttu-id="23a3e-201">ただし、WPF を使用してデータ バインディングを実行する場合は、コレクション プロパティに**ObservableCollection**を使用して、WPF がコレクションに加えられた変更を追跡できるようにすることが望ましいです。</span><span class="sxs-lookup"><span data-stu-id="23a3e-201">However, when doing data binding using WPF it is desirable to use **ObservableCollection** for collection properties so that WPF can keep track of changes made to the collections.</span></span> <span data-ttu-id="23a3e-202">この目的のために、テンプレートを変更して ObservableCollection を使用します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-202">To this end we will to modify the templates to use ObservableCollection.</span></span>

-   <span data-ttu-id="23a3e-203">ソリューション**エクスプローラー**を開き **、ProductModel.edmx**ファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-203">Open the **Solution Explorer** and find **ProductModel.edmx** file</span></span>
-   <span data-ttu-id="23a3e-204">ProductModel.edmx ファイルの下にネストされる**ProductModel.tt**ファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-204">Find the **ProductModel.tt** file which will be nested under the ProductModel.edmx file</span></span>

    ![WPF 製品モデル テンプレート](~/ef6/media/wpfproductmodeltemplate.png)

-   <span data-ttu-id="23a3e-206">ProductModel.ttファイルをダブルクリックして、Visual Studio エディターで開きます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-206">Double-click on the ProductModel.tt file to open it in the Visual Studio editor</span></span>
-   <span data-ttu-id="23a3e-207">"ICollection" の 2 つの出現箇所を検索して **、"ObservableCollection"** に置き換えます。**ICollection**</span><span class="sxs-lookup"><span data-stu-id="23a3e-207">Find and replace the two occurrences of “**ICollection**” with “**ObservableCollection**”.</span></span> <span data-ttu-id="23a3e-208">これらは、約 296 行目と 484 行目にあります。</span><span class="sxs-lookup"><span data-stu-id="23a3e-208">These are located approximately at lines 296 and 484.</span></span>
-   <span data-ttu-id="23a3e-209">"**HashSet**" の最初の出現箇所を検索して、"**観測可能なコレクション**" に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-209">Find and replace the first occurrence of “**HashSet**” with “**ObservableCollection**”.</span></span> <span data-ttu-id="23a3e-210">このオカレンスは、ほぼ 50 行目にあります。</span><span class="sxs-lookup"><span data-stu-id="23a3e-210">This occurrence is located approximately at line 50.</span></span> <span data-ttu-id="23a3e-211">コードの後半で見つかった HashSet の 2 番目のオカレンスを置き換**えないでください**。</span><span class="sxs-lookup"><span data-stu-id="23a3e-211">**Do not** replace the second occurrence of HashSet found later in the code.</span></span>
-   <span data-ttu-id="23a3e-212">"**System.Collections.Generic**" の唯一の出現を**System.Collections.ObjectModel**検索して置き換えます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-212">Find and replace the only occurrence of “**System.Collections.Generic**” with “**System.Collections.ObjectModel**”.</span></span> <span data-ttu-id="23a3e-213">これは、約 424 行目にあります。</span><span class="sxs-lookup"><span data-stu-id="23a3e-213">This is located approximately at line 424.</span></span>
-   <span data-ttu-id="23a3e-214">ProductModel.tt ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-214">Save the ProductModel.tt file.</span></span> <span data-ttu-id="23a3e-215">これにより、エンティティのコードが再生成されます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-215">This should cause the code for entities to be regenerated.</span></span> <span data-ttu-id="23a3e-216">コードが自動的に再生されない場合は、ProductModel.tt右クリックして[カスタムツールを実行]を選択します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-216">If the code does not regenerate automatically, then right click on ProductModel.tt and choose “Run Custom Tool”.</span></span>

<span data-ttu-id="23a3e-217">Category.cs ファイル (ProductModel.ttの下にネストされている) を開くと、Products コレクションの種類が **[監視可能コレクション製品&lt;&gt;**] であることがわかります。</span><span class="sxs-lookup"><span data-stu-id="23a3e-217">If you now open the Category.cs file (which is nested under ProductModel.tt) then you should see that the Products collection has the type **ObservableCollection&lt;Product&gt;**.</span></span>

<span data-ttu-id="23a3e-218">プロジェクトをコンパイルします。</span><span class="sxs-lookup"><span data-stu-id="23a3e-218">Compile the project.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="23a3e-219">遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="23a3e-219">Lazy Loading</span></span>

<span data-ttu-id="23a3e-220">**Product**クラスの**カテゴリ**クラスと**カテゴリ**プロパティの**Products**プロパティは、ナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="23a3e-220">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="23a3e-221">Entity Framework では、ナビゲーション プロパティは、2 つのエンティティ型間のリレーションシップをナビゲートする方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-221">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="23a3e-222">EF では、ナビゲーション プロパティに最初にアクセスするときに、データベースから関連エンティティを自動的に読み込むオプションが提供されます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-222">EF gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="23a3e-223">このタイプの読み込み (遅延読み込み) では、各ナビゲーション プロパティに初めてアクセスするときに、コンテンツがまだコンテキスト内にない場合は、データベースに対して別のクエリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-223">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="23a3e-224">POCO エンティティ型を使用する場合、EF は実行時に派生プロキシ型のインスタンスを作成し、クラスの仮想プロパティをオーバーライドして読み込みフックを追加することで、遅延読み込みを実現します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-224">When using POCO entity types, EF achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="23a3e-225">関連オブジェクトの遅延読み込みを取得するには、ナビゲーション プロパティ**NotOverridable**のゲッターを**パブリック**および**仮想**(Visual Basic では**オーバーライド可能**) として宣言する必要があります。 **sealed**</span><span class="sxs-lookup"><span data-stu-id="23a3e-225">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and you class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="23a3e-226">Database First ナビゲーション プロパティを使用すると、遅延読み込みを有効にするために自動的に仮想にします。</span><span class="sxs-lookup"><span data-stu-id="23a3e-226">When using Database First navigation properties are automatically made virtual to enable lazy loading.</span></span> <span data-ttu-id="23a3e-227">[コード 1] セクションでは、同じ理由でナビゲーション プロパティを仮想にすることを選択しました</span><span class="sxs-lookup"><span data-stu-id="23a3e-227">In the Code First section we chose to make the navigation properties virtual for the same reason</span></span>

## <a name="bind-object-to-controls"></a><span data-ttu-id="23a3e-228">コントロールにオブジェクトをバインド</span><span class="sxs-lookup"><span data-stu-id="23a3e-228">Bind Object to Controls</span></span>

<span data-ttu-id="23a3e-229">この WPF アプリケーションのデータ ソースとしてモデルで定義されているクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-229">Add the classes that are defined in the model as data sources for this WPF application.</span></span>

-   <span data-ttu-id="23a3e-230">ソリューション エクスプローラーで**MainWindow.xaml**をダブルクリックして、メイン フォームを開きます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-230">Double-click **MainWindow.xaml** in Solution Explorer to open the main form</span></span>
-   <span data-ttu-id="23a3e-231">メイン メニューから、[**プロジェクト ]&gt; - [新しいデータ ソースの追加] を選択します。**</span><span class="sxs-lookup"><span data-stu-id="23a3e-231">From the main menu, select **Project -&gt; Add New Data Source …**</span></span>
    <span data-ttu-id="23a3e-232">(Visual Studio 2010 では、[**データ -&gt;新しいデータ ソースの追加]** を選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23a3e-232">(in Visual Studio 2010, you need to select **Data -&gt; Add New Data Source…**)</span></span>
-   <span data-ttu-id="23a3e-233">[データ ソースの種類の選択] ウィンドウで、[**オブジェクト] を**選択し、[**次へ**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="23a3e-233">In the Choose a Data Source Typewindow, select **Object** and click **Next**</span></span>
-   <span data-ttu-id="23a3e-234">[データ オブジェクトの選択] ダイアログで **、WPFwithEFSample** を 2 回展開し、[**カテゴリ**]</span><span class="sxs-lookup"><span data-stu-id="23a3e-234">In the Select the Data Objects dialog, unfold the **WPFwithEFSample** two times and select **Category**</span></span>  
    <span data-ttu-id="23a3e-235">\***商品**データ ソースを選択する**Product**必要はありません。 **Product**\*</span><span class="sxs-lookup"><span data-stu-id="23a3e-235">*There is no need to select the **Product** data source, because we will get to it through the **Product**’s property on the **Category** data source*</span></span>  

    ![データ オブジェクトの選択](~/ef6/media/selectdataobjects.png)

-   <span data-ttu-id="23a3e-237">**[完了]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="23a3e-237">Click **Finish.**</span></span>
-   <span data-ttu-id="23a3e-238">[データ ソース] ウィンドウが [MainWindow.xaml] ウィンドウの横に表示*される [データ ソース] ウィンドウが表示されていない場合は、[**表示 -&gt;その他の Windows-&gt;データ ソース**] を選択します。*</span><span class="sxs-lookup"><span data-stu-id="23a3e-238">The Data Sources window is opened next to the MainWindow.xaml window *If the Data Sources window is not showing up, select **View -&gt; Other Windows-&gt; Data Sources***</span></span>
-   <span data-ttu-id="23a3e-239">ピン アイコンを押すと、[データ ソース] ウィンドウが自動的に非表示になっていません。</span><span class="sxs-lookup"><span data-stu-id="23a3e-239">Press the pin icon, so the Data Sources window does not auto hide.</span></span> <span data-ttu-id="23a3e-240">ウィンドウが既に表示されている場合は、更新ボタンを押す必要があります。</span><span class="sxs-lookup"><span data-stu-id="23a3e-240">You may need to hit the refresh button if the window was already visible.</span></span>

    ![ソリューション エクスプローラー](~/ef6/media/datasources.png)

-   <span data-ttu-id="23a3e-242"> **[カテゴリ]** データ ソースを選択し、フォーム上にドラッグします。</span><span class="sxs-lookup"><span data-stu-id="23a3e-242">Select the **Category** data source and drag it on the form.</span></span>

<span data-ttu-id="23a3e-243">このソースをドラッグすると、次のことが起こりました。</span><span class="sxs-lookup"><span data-stu-id="23a3e-243">The following happened when we dragged this source:</span></span>

-   <span data-ttu-id="23a3e-244">**リソース**と**カテゴリデータ グリッド**コントロールが XAML に追加されました。</span><span class="sxs-lookup"><span data-stu-id="23a3e-244">The **categoryViewSource** resource and the **categoryDataGrid** control were added to XAML</span></span> 
-   <span data-ttu-id="23a3e-245">親グリッド要素の DataContext プロパティが "{静的リソース**カテゴリビューソース**} に設定されました。</span><span class="sxs-lookup"><span data-stu-id="23a3e-245">The DataContext property on the parent Grid element was set to "{StaticResource **categoryViewSource** }".</span></span><span data-ttu-id="23a3e-246">**categoryViewSource**リソースは、外部\\の親グリッド要素のバインディング ソースとして機能します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-246"> The **categoryViewSource** resource serves as a binding source for the outer\\parent Grid element.</span></span> <span data-ttu-id="23a3e-247">内部グリッド要素は、親グリッドから DataContext 値を継承します (カテゴリデータグリッドの ItemsSource プロパティが "{バインディング}" に設定されています)。</span><span class="sxs-lookup"><span data-stu-id="23a3e-247">The inner Grid elements then inherit the DataContext value from the parent Grid (the categoryDataGrid’s ItemsSource property is set to "{Binding}")</span></span>

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

## <a name="adding-a-details-grid"></a><span data-ttu-id="23a3e-248">詳細グリッドの追加</span><span class="sxs-lookup"><span data-stu-id="23a3e-248">Adding a Details Grid</span></span>

<span data-ttu-id="23a3e-249">カテゴリを表示するグリッドができたので、関連する製品を表示するための詳細グリッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-249">Now that we have a grid to display Categories let's add a details grid to display the associated Products.</span></span>

-   <span data-ttu-id="23a3e-250">[商品] **プロパティを** **[カテゴリ]** データ ソースの下から選択し、フォーム上にドラッグします。</span><span class="sxs-lookup"><span data-stu-id="23a3e-250">Select the **Products** property from under the **Category** data source and drag it on the form.</span></span>
    -   <span data-ttu-id="23a3e-251">**カテゴリ製品ビューソース**リソースと**製品データ グリッド**が XAML に追加されます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-251">The **categoryProductsViewSource** resource and **productDataGrid** grid are added to XAML</span></span>
    -   <span data-ttu-id="23a3e-252">このリソースのバインド パスは[製品]に設定されています。</span><span class="sxs-lookup"><span data-stu-id="23a3e-252">The binding path for this resource is set to Products</span></span>
    -   <span data-ttu-id="23a3e-253">WPF データ バインディング フレームワークは、選択したカテゴリに関連する製品のみが**ProductDataGrid**に表示されるようにします。</span><span class="sxs-lookup"><span data-stu-id="23a3e-253">WPF data-binding framework ensures that only Products related to the selected Category show up in **productDataGrid**</span></span>
-   <span data-ttu-id="23a3e-254">ツールボックスから、フォームに**ボタン**をドラッグします。</span><span class="sxs-lookup"><span data-stu-id="23a3e-254">From the Toolbox, drag **Button** on to the form.</span></span> <span data-ttu-id="23a3e-255">**Name**プロパティを**ボタンに設定し、\*\*\*\*コンテンツ**プロパティを [ 保存 ] に**設定**します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-255">Set the **Name** property to **buttonSave** and the **Content** property to **Save**.</span></span>

<span data-ttu-id="23a3e-256">フォームは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="23a3e-256">The form should look similar to this:</span></span>

![Designer](~/ef6/media/designer.png) 

## <a name="add-code-that-handles-data-interaction"></a><span data-ttu-id="23a3e-258">データの相互作用を処理するコードを追加する</span><span class="sxs-lookup"><span data-stu-id="23a3e-258">Add Code that Handles Data Interaction</span></span>

<span data-ttu-id="23a3e-259">メイン ウィンドウにイベント ハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-259">It's time to add some event handlers to the main window.</span></span>

-   <span data-ttu-id="23a3e-260">XAML ウィンドウで、**&lt;ウィンドウ**要素をクリックすると、メイン ウィンドウが選択されます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-260">In the XAML window, click on the **&lt;Window** element, this selects the main window</span></span>
-   <span data-ttu-id="23a3e-261">[**プロパティ]** ウィンドウの右上にある **[イベント**] を選択し、[**読み込み]** ラベルの右にあるテキスト ボックスをダブルクリックします。</span><span class="sxs-lookup"><span data-stu-id="23a3e-261">In the **Properties** window choose **Events** at the top right, then double-click the text box to right of the **Loaded** label</span></span>

    ![メイン ウィンドウのプロパティ](~/ef6/media/mainwindowproperties.png)

-   <span data-ttu-id="23a3e-263">また、デザイナーの **[保存**] ボタンをダブルクリックして、[保存] ボタンの**Click**イベントを追加します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-263">Also add the **Click** event for the **Save** button by double-clicking the Save button in the designer.</span></span> 

<span data-ttu-id="23a3e-264">これにより、フォームの分離コードが表示され、コードを編集して ProductContext を使用してデータ アクセスを実行します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-264">This brings you to the code behind for the form, we'll now edit the code to use the ProductContext to perform data access.</span></span> <span data-ttu-id="23a3e-265">次に示すように、メイン ウィンドウのコードを更新します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-265">Update the code for the MainWindow as shown below.</span></span>

<span data-ttu-id="23a3e-266">このコードでは、実行時間の長いインスタンス**を宣言**します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-266">The code declares a long-running instance of **ProductContext**.</span></span> <span data-ttu-id="23a3e-267">**オブジェクト**は、クエリを実行し、データベースにデータを保存するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-267">The **ProductContext** object is used to query and save data to the database.</span></span> <span data-ttu-id="23a3e-268">その後、オーバーライドされた**OnClosing**メソッドから、**インスタンス**の**Dispose()** が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-268">The **Dispose()** on the **ProductContext** instance is then called from the overridden **OnClosing** method.</span></span><span data-ttu-id="23a3e-269">コード コメントは、コードの動作に関する詳細を示します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-269"> The code comments provide details about what the code does.</span></span>

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

## <a name="test-the-wpf-application"></a><span data-ttu-id="23a3e-270">WPF アプリケーションのテスト</span><span class="sxs-lookup"><span data-stu-id="23a3e-270">Test the WPF Application</span></span>

-   <span data-ttu-id="23a3e-271">アプリケーションをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-271">Compile and run the application.</span></span> <span data-ttu-id="23a3e-272">コードファーストを使用した場合は **、WPFwithEFSample.ProductContext**データベースが作成されていることがわかります。</span><span class="sxs-lookup"><span data-stu-id="23a3e-272">If you used Code First, then you will see that a **WPFwithEFSample.ProductContext** database is created for you.</span></span>
-   <span data-ttu-id="23a3e-273">一番上のグリッドにカテゴリ名を入力し、下部のグリッドに商品名を*入力します ID 列には何も入力しないでください。*</span><span class="sxs-lookup"><span data-stu-id="23a3e-273">Enter a category name in the top grid and product names in the bottom grid *Do not enter anything in ID columns, because the primary key is generated by the database*</span></span>

    ![新しいカテゴリと製品を含むメインウィンドウ](~/ef6/media/screen1.png)

-   <span data-ttu-id="23a3e-275">**[保存**] ボタンを押して、データをデータベースに保存します。</span><span class="sxs-lookup"><span data-stu-id="23a3e-275">Press the **Save** button to save the data to the database</span></span>

<span data-ttu-id="23a3e-276">呼び出しの**後に、** データベースで生成された値が ID に設定されます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-276">After the call to DbContext’s **SaveChanges()**, the IDs are populated with the database generated values.</span></span> <span data-ttu-id="23a3e-277">**SaveChanges()** の後**に Refresh()** を呼び出したので **、DataGrid**コントロールも新しい値で更新されます。</span><span class="sxs-lookup"><span data-stu-id="23a3e-277">Because we called **Refresh()** after **SaveChanges()** the **DataGrid** controls are updated with the new values as well.</span></span>

![ID が設定されたメイン ウィンドウ](~/ef6/media/screen2.png)

## <a name="additional-resources"></a><span data-ttu-id="23a3e-279">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="23a3e-279">Additional Resources</span></span>

<span data-ttu-id="23a3e-280">WPF を使用したコレクションへのデータ バインディングの詳細については、WPF のドキュメントの[このトピック](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23a3e-280">To learn more about data binding to collections using WPF, see [this topic](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections) in the WPF documentation.</span></span>  
