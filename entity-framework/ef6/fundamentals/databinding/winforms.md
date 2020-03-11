---
title: WinForms を使用したデータバインド-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 80fc5062-2f1c-4dbd-ab6e-b99496784b36
ms.openlocfilehash: 4b3eee20ff238864b94ef4edfb97c1bae0713300
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416205"
---
# <a name="databinding-with-winforms"></a><span data-ttu-id="e1d5d-102">WinForms を使用したデータバインド</span><span class="sxs-lookup"><span data-stu-id="e1d5d-102">Databinding with WinForms</span></span>
<span data-ttu-id="e1d5d-103">このステップバイステップチュートリアルでは、POCO 型を "マスター/詳細" フォームでウィンドウフォーム (WinForms) コントロールにバインドする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-103">This step-by-step walkthrough shows how to bind POCO types to Window Forms (WinForms) controls in a “master-detail" form.</span></span> <span data-ttu-id="e1d5d-104">アプリケーションでは、Entity Framework を使用して、データベースのデータをオブジェクトに読み込み、変更を追跡し、データベースにデータを保持します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-104">The application uses Entity Framework to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="e1d5d-105">このモデルでは、1対多のリレーションシップに参加する2つの型 (Category (principal\\master) と Product (依存\\詳細) が定義されています。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-105">The model defines two types that participate in one-to-many relationship: Category (principal\\master) and Product (dependent\\detail).</span></span> <span data-ttu-id="e1d5d-106">次に、Visual Studio ツールを使用して、モデルで定義されている型を WinForms コントロールにバインドします。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-106">Then, the Visual Studio tools are used to bind the types defined in the model to the WinForms controls.</span></span> <span data-ttu-id="e1d5d-107">WinForms データバインディングフレームワークを使用すると、関連するオブジェクト間のナビゲーションが可能になります。マスタービューで行を選択すると、対応する子データで詳細ビューが更新されます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-107">The WinForms data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="e1d5d-108">このチュートリアルのスクリーンショットとコードリストは Visual Studio 2013 から取得されていますが、Visual Studio 2012 または Visual Studio 2010 を使用してこのチュートリアルを完了することができます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-108">The screen shots and code listings in this walkthrough are taken from Visual Studio 2013 but you can complete this walkthrough with Visual Studio 2012 or Visual Studio 2010.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="e1d5d-109">前提条件</span><span class="sxs-lookup"><span data-stu-id="e1d5d-109">Pre-Requisites</span></span>

<span data-ttu-id="e1d5d-110">このチュートリアルを完了するには、Visual Studio 2013、Visual Studio 2012、または Visual Studio 2010 がインストールされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-110">You need to have Visual Studio 2013, Visual Studio 2012 or Visual Studio 2010 installed to complete this walkthrough.</span></span>

<span data-ttu-id="e1d5d-111">Visual Studio 2010 を使用している場合は、NuGet もインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-111">If you are using Visual Studio 2010, you also have to install NuGet.</span></span> <span data-ttu-id="e1d5d-112">詳細については、「 [NuGet のインストール](https://docs.nuget.org/docs/start-here/installing-nuget)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-112">For more information, see [Installing NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).</span></span>

## <a name="create-the-application"></a><span data-ttu-id="e1d5d-113">アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="e1d5d-113">Create the Application</span></span>

-   <span data-ttu-id="e1d5d-114">Visual Studio を開きます</span><span class="sxs-lookup"><span data-stu-id="e1d5d-114">Open Visual Studio</span></span>
-   <span data-ttu-id="e1d5d-115">**ファイル&gt; 新規&gt; プロジェクト....**</span><span class="sxs-lookup"><span data-stu-id="e1d5d-115">**File -&gt; New -&gt; Project….**</span></span>
-   <span data-ttu-id="e1d5d-116">左側のウィンドウで **[windows]** を選択し、右側のウィンドウで**windows フォームアプリケーション**を選択します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-116">Select **Windows** in the left pane and **Windows FormsApplication** in the right pane</span></span>
-   <span data-ttu-id="e1d5d-117">名前として「 **Winフォーム Withefsample** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-117">Enter **WinFormswithEFSample** as the name</span></span>
-   <span data-ttu-id="e1d5d-118">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-118">Select **OK**</span></span>

## <a name="install-the-entity-framework-nuget-package"></a><span data-ttu-id="e1d5d-119">Entity Framework NuGet パッケージをインストールする</span><span class="sxs-lookup"><span data-stu-id="e1d5d-119">Install the Entity Framework NuGet package</span></span>

-   <span data-ttu-id="e1d5d-120">ソリューションエクスプローラーで、 **Winフォーム Withefsample**プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-120">In Solution Explorer, right-click on the **WinFormswithEFSample** project</span></span>
-   <span data-ttu-id="e1d5d-121">**[NuGet パッケージの管理...]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-121">Select **Manage NuGet Packages…**</span></span>
-   <span data-ttu-id="e1d5d-122">NuGet パッケージの管理 ダイアログで、**オンライン** タブを選択し、 **entityframework**パッケージを選択します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-122">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
-   <span data-ttu-id="e1d5d-123">**[インストール]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-123">Click **Install**</span></span>  
    > [!NOTE]
    > <span data-ttu-id="e1d5d-124">EntityFramework アセンブリに加えて、System.componentmodel への参照も追加されます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-124">In addition to the EntityFramework assembly a reference to System.ComponentModel.DataAnnotations is also added.</span></span> <span data-ttu-id="e1d5d-125">プロジェクトに system.object への参照が含まれている場合は、EntityFramework パッケージのインストール時に削除されます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-125">If the project has a reference to System.Data.Entity, then it will be removed when the EntityFramework package is installed.</span></span> <span data-ttu-id="e1d5d-126">Entity Framework 6 アプリケーションでは、system.object アセンブリは使用されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-126">The System.Data.Entity assembly is no longer used for Entity Framework 6 applications.</span></span>

## <a name="implementing-ilistsource-for-collections"></a><span data-ttu-id="e1d5d-127">コレクションの IListSource の実装</span><span class="sxs-lookup"><span data-stu-id="e1d5d-127">Implementing IListSource for Collections</span></span>

<span data-ttu-id="e1d5d-128">コレクションプロパティは、Windows フォームを使用するときに、並べ替えを使用した双方向のデータバインディングを有効にするために、IListSource インターフェイスを実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-128">Collection properties must implement the IListSource interface to enable two-way data binding with sorting when using Windows Forms.</span></span> <span data-ttu-id="e1d5d-129">これを行うには、System.collections.objectmodel.observablecollection を拡張して IListSource 機能を追加します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-129">To do this we are going to extend ObservableCollection to add IListSource functionality.</span></span>

-   <span data-ttu-id="e1d5d-130">**ObservableListSource**クラスをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-130">Add an **ObservableListSource** class to the project:</span></span>
    -   <span data-ttu-id="e1d5d-131">プロジェクト名を右クリックします。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-131">Right-click on the project name</span></span>
    -   <span data-ttu-id="e1d5d-132">[**新しい項目の追加&gt;** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-132">Select **Add -&gt; New Item**</span></span>
    -   <span data-ttu-id="e1d5d-133">**[クラス]** を選択し、クラス名として「 **ObservableListSource** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-133">Select **Class** and enter **ObservableListSource** for the class name</span></span>
-   <span data-ttu-id="e1d5d-134">既定で生成されるコードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-134">Replace the code generated by default with the following code:</span></span>

<span data-ttu-id="e1d5d-135">*このクラスは、双方向のデータバインディングと並べ替えを有効にします。クラスは、System.collections.objectmodel.observablecollection&lt;T&gt; から派生し、IListSource の明示的な実装を追加します。IListSource の GetList () メソッドを実装すると、System.collections.objectmodel.observablecollection との同期を維持した状態で IBindingList 実装が返されます。ToBindingList によって生成される IBindingList 実装は、並べ替えをサポートしています。ToBindingList 拡張メソッドは EntityFramework アセンブリで定義されています。*</span><span class="sxs-lookup"><span data-stu-id="e1d5d-135">*This class enables two-way data binding as well as sorting. The class derives from ObservableCollection&lt;T&gt; and adds an explicit implementation of IListSource. The GetList() method of IListSource is implemented to return an IBindingList implementation that stays in sync with the ObservableCollection. The IBindingList implementation generated by ToBindingList supports sorting. The ToBindingList extension method is defined in the EntityFramework assembly.*</span></span>

``` csharp
    using System.Collections;
    using System.Collections.Generic;
    using System.Collections.ObjectModel;
    using System.ComponentModel;
    using System.Diagnostics.CodeAnalysis;
    using System.Data.Entity;

    namespace WinFormswithEFSample
    {
        public class ObservableListSource<T> : ObservableCollection<T>, IListSource
            where T : class
        {
            private IBindingList _bindingList;

            bool IListSource.ContainsListCollection { get { return false; } }

            IList IListSource.GetList()
            {
                return _bindingList ?? (_bindingList = this.ToBindingList());
            }
        }
    }
```

## <a name="define-a-model"></a><span data-ttu-id="e1d5d-136">モデルの定義</span><span class="sxs-lookup"><span data-stu-id="e1d5d-136">Define a Model</span></span>

<span data-ttu-id="e1d5d-137">このチュートリアルでは、Code First または EF デザイナーを使用してモデルを実装することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-137">In this walkthrough you can chose to implement a model using Code First or the EF Designer.</span></span> <span data-ttu-id="e1d5d-138">次の2つのセクションのいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-138">Complete one of the two following sections.</span></span>

### <a name="option-1-define-a-model-using-code-first"></a><span data-ttu-id="e1d5d-139">オプション 1: Code First を使用してモデルを定義する</span><span class="sxs-lookup"><span data-stu-id="e1d5d-139">Option 1: Define a Model using Code First</span></span>

<span data-ttu-id="e1d5d-140">このセクションでは、Code First を使用してモデルとそれに関連付けられたデータベースを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-140">This section shows how to create a model and its associated database using Code First.</span></span> <span data-ttu-id="e1d5d-141">EF デザイナーを使用してデータベースからモデルをリバースエンジニアリングする場合に Database First を使用する場合は、次のセクション (「**オプション 2: Database First を使用してモデルを定義**する」) に進んでください。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-141">Skip to the next section (**Option 2: Define a model using Database First)** if you would rather use Database First to reverse engineer your model from a database using the EF designer</span></span>

<span data-ttu-id="e1d5d-142">Code First 開発を使用する場合は、通常、概念 (ドメイン) モデルを定義する .NET Framework クラスを記述することから始めます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-142">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span>

-   <span data-ttu-id="e1d5d-143">新しい**製品**クラスをプロジェクトに追加する</span><span class="sxs-lookup"><span data-stu-id="e1d5d-143">Add a new **Product** class to project</span></span>
-   <span data-ttu-id="e1d5d-144">既定で生成されるコードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-144">Replace the code generated by default with the following code:</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace WinFormswithEFSample
    {
        public class Product
        {
            public int ProductId { get; set; }
            public string Name { get; set; }

            public int CategoryId { get; set; }
            public virtual Category Category { get; set; }
        }
    }
```

-   <span data-ttu-id="e1d5d-145">**カテゴリ**クラスをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-145">Add a **Category** class to the project.</span></span>
-   <span data-ttu-id="e1d5d-146">既定で生成されるコードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-146">Replace the code generated by default with the following code:</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace WinFormswithEFSample
    {
        public class Category
        {
            private readonly ObservableListSource<Product> _products =
                    new ObservableListSource<Product>();

            public int CategoryId { get; set; }
            public string Name { get; set; }
            public virtual ObservableListSource<Product> Products { get { return _products; } }
        }
    }
```

<span data-ttu-id="e1d5d-147">エンティティを定義するだけでなく、 **Dbcontext**から派生するクラスを定義し、 **Dbcontext&lt;TEntity&gt;** プロパティを公開する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-147">In addition to defining entities, you need to define a class that derives from **DbContext** and exposes **DbSet&lt;TEntity&gt;** properties.</span></span> <span data-ttu-id="e1d5d-148">**Dbset**プロパティを使用すると、モデルに含める型をコンテキストに認識させることができます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-148">The **DbSet** properties let the context know which types you want to include in the model.</span></span> <span data-ttu-id="e1d5d-149">**Dbcontext**および**Dbcontext**型は entityframework アセンブリで定義されています。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-149">The **DbContext** and **DbSet** types are defined in the EntityFramework assembly.</span></span>

<span data-ttu-id="e1d5d-150">DbContext の派生型のインスタンスは、実行時にエンティティオブジェクトを管理します。これには、データベースからのデータを使用したオブジェクトの読み込み、変更の追跡、およびデータベースへのデータの永続化が含まれます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-150">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

-   <span data-ttu-id="e1d5d-151">新しい**Productcontext**クラスをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-151">Add a new **ProductContext** class to the project.</span></span>
-   <span data-ttu-id="e1d5d-152">既定で生成されるコードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-152">Replace the code generated by default with the following code:</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Linq;
    using System.Text;

    namespace WinFormswithEFSample
    {
        public class ProductContext : DbContext
        {
            public DbSet<Category> Categories { get; set; }
            public DbSet<Product> Products { get; set; }
        }
    }
```

<span data-ttu-id="e1d5d-153">プロジェクトをコンパイルします。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-153">Compile the project.</span></span>

### <a name="option-2-define-a-model-using-database-first"></a><span data-ttu-id="e1d5d-154">オプション 2: Database First を使用してモデルを定義する</span><span class="sxs-lookup"><span data-stu-id="e1d5d-154">Option 2: Define a model using Database First</span></span>

<span data-ttu-id="e1d5d-155">このセクションでは、Database First を使用して、EF デザイナーを使用してデータベースからモデルをリバースエンジニアリングする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-155">This section shows how to use Database First to reverse engineer your model from a database using the EF designer.</span></span> <span data-ttu-id="e1d5d-156">前のセクション (**オプション 1: Code First を使用してモデルを定義する)** を完了している場合は、このセクションをスキップし、「**遅延読み込み**」セクションに直接移動します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-156">If you completed the previous section (**Option 1: Define a model using Code First)**, then skip this section and go straight to the **Lazy Loading** section.</span></span>

#### <a name="create-an-existing-database"></a><span data-ttu-id="e1d5d-157">既存のデータベースを作成する</span><span class="sxs-lookup"><span data-stu-id="e1d5d-157">Create an Existing Database</span></span>

<span data-ttu-id="e1d5d-158">通常、既存のデータベースを対象とする場合は、既に作成されますが、このチュートリアルでは、にアクセスするデータベースを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-158">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="e1d5d-159">Visual Studio と共にインストールされるデータベースサーバーは、インストールされている Visual Studio のバージョンによって異なります。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-159">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="e1d5d-160">Visual Studio 2010 を使用している場合は、SQL Express データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-160">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="e1d5d-161">Visual Studio 2012 を使用している場合は、 [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx)データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-161">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) database.</span></span>

<span data-ttu-id="e1d5d-162">では、データベースを生成してみましょう。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-162">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="e1d5d-163">**ビュー&gt; サーバーエクスプローラー**</span><span class="sxs-lookup"><span data-stu-id="e1d5d-163">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="e1d5d-164">**[データ接続]** を右クリックし&gt; [接続の追加] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-164">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="e1d5d-165">サーバーエクスプローラーからデータベースに接続していない場合は、データソースとして Microsoft SQL Server を選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-165">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![データ ソースの変更](~/ef6/media/changedatasource.png)

-   <span data-ttu-id="e1d5d-167">インストールされているものに応じて LocalDB または SQL Express に接続し、データベース名として「 **Products** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-167">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **Products** as the database name</span></span>

    ![接続 LocalDB の追加](~/ef6/media/addconnectionlocaldb.png)

    ![接続 Express の追加](~/ef6/media/addconnectionexpress.png)

-   <span data-ttu-id="e1d5d-170">[ **OK]** を選択すると、新しいデータベースを作成するかどうかを確認するメッセージが表示されます。 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-170">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![データベースの作成](~/ef6/media/createdatabase.png)

-   <span data-ttu-id="e1d5d-172">新しいデータベースがサーバーエクスプローラーに表示され、右クリックして **[新しいクエリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-172">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="e1d5d-173">次の SQL を新しいクエリにコピーし、クエリを右クリックして、 **[実行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-173">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

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

#### <a name="reverse-engineer-model"></a><span data-ttu-id="e1d5d-174">リバースエンジニアリングモデル</span><span class="sxs-lookup"><span data-stu-id="e1d5d-174">Reverse Engineer Model</span></span>

<span data-ttu-id="e1d5d-175">ここでは、Visual Studio の一部として含まれている Entity Framework Designer を使用して、モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-175">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="e1d5d-176">**プロジェクト-新しい項目の追加&gt;...**</span><span class="sxs-lookup"><span data-stu-id="e1d5d-176">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="e1d5d-177">左側のメニューから **データ** を選択し、ADO.NET をクリックし**Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="e1d5d-177">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="e1d5d-178">名前として「 **Productmodel** 」と入力し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-178">Enter **ProductModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="e1d5d-179">**Entity Data Model ウィザード**が起動します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-179">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="e1d5d-180">**[データベースから生成]** を選択し、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-180">Select **Generate from Database** and click **Next**</span></span>

    ![Modelcontents を行う](~/ef6/media/choosemodelcontents.png)

-   <span data-ttu-id="e1d5d-182">最初のセクションで作成したデータベースへの接続を選択し、接続文字列の名前として「 **Productcontext** 」と入力して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-182">Select the connection to the database you created in the first section, enter **ProductContext** as the name of the connection string and click **Next**</span></span>

    ![接続の選択](~/ef6/media/chooseyourconnection.png)

-   <span data-ttu-id="e1d5d-184">[テーブル] の横にあるチェックボックスをオンにしてすべてのテーブルをインポートし、[完了] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-184">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![オブジェクトの選択](~/ef6/media/chooseyourobjects.png)

<span data-ttu-id="e1d5d-186">リバースエンジニアリングプロセスが完了すると、新しいモデルがプロジェクトに追加され、Entity Framework Designer で表示できるように開かれます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-186">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="e1d5d-187">App.config ファイルも、データベースの接続の詳細と共にプロジェクトに追加されています。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-187">An App.config file has also been added to your project with the connection details for the database.</span></span>

#### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="e1d5d-188">Visual Studio 2010 の追加手順</span><span class="sxs-lookup"><span data-stu-id="e1d5d-188">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="e1d5d-189">Visual Studio 2010 で作業している場合は、EF6 コード生成を使用するように EF デザイナーを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-189">If you are working in Visual Studio 2010 then you will need to update the EF designer to use EF6 code generation.</span></span>

-   <span data-ttu-id="e1d5d-190">EF デザイナーでモデルの空の場所を右クリックし、 **[コード生成項目の追加...]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-190">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="e1d5d-191">左側のメニューから **[オンラインテンプレート]** を選択し、 **dbcontext**を検索します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-191">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="e1d5d-192">**C\#の EF 6.X DbContext ジェネレーター**を選択し、名前として「製品**モデル**」と入力して、[追加] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-192">Select the **EF 6.x DbContext Generator for C\#,** enter **ProductsModel** as the name and click Add</span></span>

#### <a name="updating-code-generation-for-data-binding"></a><span data-ttu-id="e1d5d-193">データバインディングのコード生成の更新</span><span class="sxs-lookup"><span data-stu-id="e1d5d-193">Updating code generation for data binding</span></span>

<span data-ttu-id="e1d5d-194">EF は T4 テンプレートを使用して、モデルからコードを生成します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-194">EF generates code from your model using T4 templates.</span></span> <span data-ttu-id="e1d5d-195">Visual Studio に付属しているテンプレート、または Visual Studio ギャラリーからダウンロードしたテンプレートは、汎用的な使用を目的としています。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-195">The templates shipped with Visual Studio or downloaded from the Visual Studio gallery are intended for general purpose use.</span></span> <span data-ttu-id="e1d5d-196">これは、これらのテンプレートから生成されたエンティティに単純な ICollection&lt;T&gt; プロパティがあることを意味します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-196">This means that the entities generated from these templates have simple ICollection&lt;T&gt; properties.</span></span> <span data-ttu-id="e1d5d-197">ただし、データバインディングを実行する場合は、IListSource を実装するコレクションプロパティを設定することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-197">However, when doing data binding it is desirable to have collection properties that implement IListSource.</span></span> <span data-ttu-id="e1d5d-198">このため、上記の ObservableListSource クラスを作成したので、このクラスを使用するようにテンプレートを変更します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-198">This is why we created the ObservableListSource class above and we are now going to modify the templates to make use of this class.</span></span>

-   <span data-ttu-id="e1d5d-199">**ソリューションエクスプローラー**を開き、 **productmodel. .edmx**ファイルを検索します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-199">Open the **Solution Explorer** and find **ProductModel.edmx** file</span></span>
-   <span data-ttu-id="e1d5d-200">ProductModel. .edmx ファイルの下に入れ子になっている**ProductModel.tt**ファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-200">Find the **ProductModel.tt** file which will be nested under the ProductModel.edmx file</span></span>

    ![製品モデルテンプレート](~/ef6/media/productmodeltemplate.png)

-   <span data-ttu-id="e1d5d-202">ProductModel.tt ファイルをダブルクリックして、Visual Studio エディターで開きます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-202">Double-click on the ProductModel.tt file to open it in the Visual Studio editor</span></span>
-   <span data-ttu-id="e1d5d-203">"**ICollection**" の2つの出現箇所を検索し、"**ObservableListSource**" に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-203">Find and replace the two occurrences of “**ICollection**” with “**ObservableListSource**”.</span></span> <span data-ttu-id="e1d5d-204">これらは、約296と484にあります。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-204">These are located at approximately lines 296 and 484.</span></span>
-   <span data-ttu-id="e1d5d-205">最初に出現する "**HashSet**" を検索し、"**ObservableListSource**" に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-205">Find and replace the first occurrence of “**HashSet**” with “**ObservableListSource**”.</span></span> <span data-ttu-id="e1d5d-206">この現象は、約50行目にあります。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-206">This occurrence is located at approximately line 50.</span></span> <span data-ttu-id="e1d5d-207">コードの後半で見つかった HashSet の2回目の出現を置き換え**ないでください**。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-207">**Do not** replace the second occurrence of HashSet found later in the code.</span></span>
-   <span data-ttu-id="e1d5d-208">ProductModel.tt ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-208">Save the ProductModel.tt file.</span></span> <span data-ttu-id="e1d5d-209">これにより、エンティティのコードが再生成されます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-209">This should cause the code for entities to be regenerated.</span></span> <span data-ttu-id="e1d5d-210">コードが自動的に再生成されない場合は、ProductModel.tt を右クリックし、[カスタムツールの実行] を選択します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-210">If the code does not regenerate automatically, then right click on ProductModel.tt and choose “Run Custom Tool”.</span></span>

<span data-ttu-id="e1d5d-211">これで、Category.cs ファイル (ProductModel.tt の下に入れ子になっています) を開くと、Products コレクションの type が**ObservableListSource&lt;Product&gt;** になっていることがわかります。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-211">If you now open the Category.cs file (which is nested under ProductModel.tt) then you should see that the Products collection has the type **ObservableListSource&lt;Product&gt;**.</span></span>

<span data-ttu-id="e1d5d-212">プロジェクトをコンパイルします。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-212">Compile the project.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="e1d5d-213">遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="e1d5d-213">Lazy Loading</span></span>

<span data-ttu-id="e1d5d-214">**Product**クラスの**Category**クラスおよび**category**プロパティの**Products**プロパティは、ナビゲーションプロパティです。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-214">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="e1d5d-215">Entity Framework では、ナビゲーションプロパティを使用して、2つのエンティティ型間のリレーションシップをナビゲートすることができます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-215">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="e1d5d-216">EF では、ナビゲーションプロパティに初めてアクセスするときに、関連エンティティをデータベースから自動的に読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-216">EF gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="e1d5d-217">この種類の読み込み (遅延読み込みと呼ばれます) を使用すると、各ナビゲーションプロパティに初めてアクセスしたときに、コンテンツがコンテキスト内に存在しない場合、データベースに対して個別のクエリが実行されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-217">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="e1d5d-218">POCO エンティティ型を使用する場合、EF は、実行時に派生プロキシ型のインスタンスを作成し、クラスの仮想プロパティをオーバーライドして読み込みフックを追加することにより、遅延読み込みを実現します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-218">When using POCO entity types, EF achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="e1d5d-219">関連オブジェクトの遅延読み込みを行うには、ナビゲーションプロパティ getter を**public**および**virtual** (Visual Basic で**オーバーライド**可能) として宣言し、クラスを**sealed** (Visual Basic では**NotOverridable** ) にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-219">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and you class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="e1d5d-220">Database First を使用すると、遅延読み込みを有効にするために、ナビゲーションプロパティが自動的に仮想化されます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-220">When using Database First navigation properties are automatically made virtual to enable lazy loading.</span></span> <span data-ttu-id="e1d5d-221">Code First セクションでは、ナビゲーションプロパティを同じ理由で仮想にすることを選択しました。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-221">In the Code First section we chose to make the navigation properties virtual for the same reason</span></span>

## <a name="bind-object-to-controls"></a><span data-ttu-id="e1d5d-222">コントロールへのオブジェクトのバインド</span><span class="sxs-lookup"><span data-stu-id="e1d5d-222">Bind Object to Controls</span></span>

<span data-ttu-id="e1d5d-223">この WinForms アプリケーションのデータソースとして、モデルで定義されているクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-223">Add the classes that are defined in the model as data sources for this WinForms application.</span></span>

-   <span data-ttu-id="e1d5d-224">メインメニューから、プロジェクト、**新しいデータソースの追加** の&gt; を選択します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-224">From the main menu, select **Project -&gt; Add New Data Source …**</span></span>
    <span data-ttu-id="e1d5d-225">(Visual Studio 2010 では、[データ] を選択する必要があります **。&gt; 新しいデータソースの追加...** )</span><span class="sxs-lookup"><span data-stu-id="e1d5d-225">(in Visual Studio 2010, you need to select **Data -&gt; Add New Data Source…**)</span></span>
-   <span data-ttu-id="e1d5d-226">データソースの種類を選択 ウィンドウで、**オブジェクト** を選択し、**次へ** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-226">In the Choose a Data Source Type window, select **Object** and click **Next**</span></span>
-   <span data-ttu-id="e1d5d-227">[データオブジェクトの選択] ダイアログで、 **Winフォーム Withefsample**を2回展開し、 **[カテゴリ]** を選択します。製品データソースを選択する必要はありません。これは、カテゴリデータソースで製品のプロパティを使用して取得するからです。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-227">In the Select the Data Objects dialog, unfold the **WinFormswithEFSample** two times and select **Category** There is no need to select the Product data source, because we will get to it through the Product’s property on the Category data source.</span></span>

    ![Data Source](~/ef6/media/datasource.png)

-   <span data-ttu-id="e1d5d-229">**[完了]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-229">Click **Finish.**</span></span>
    <span data-ttu-id="e1d5d-230">[データソース] ウィンドウが表示されていない場合は、[**表示-&gt; その他の Windows-&gt; データソース**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-230">If the Data Sources window is not showing up, select **View -&gt; Other Windows-&gt; Data Sources**</span></span>
-   <span data-ttu-id="e1d5d-231">[データソース] ウィンドウが自動的に非表示にならないように、ピンアイコンを押します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-231">Press the pin icon, so the Data Sources window does not auto hide.</span></span> <span data-ttu-id="e1d5d-232">ウィンドウが既に表示されている場合は、[更新] ボタンをクリックしなければならないことがあります。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-232">You may need to hit the refresh button if the window was already visible.</span></span>

    ![データソース2](~/ef6/media/datasource2.png)

-   <span data-ttu-id="e1d5d-234">ソリューションエクスプローラーで、 **Form1.cs**ファイルをダブルクリックして、デザイナーでメインフォームを開きます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-234">In Solution Explorer, double-click the **Form1.cs** file to open the main form in designer.</span></span>
-   <span data-ttu-id="e1d5d-235">**カテゴリ**データソースを選択し、フォーム上にドラッグします。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-235">Select the **Category** data source and drag it on the form.</span></span> <span data-ttu-id="e1d5d-236">既定では、新しい DataGridView (**カテゴリ DataGridView**) コントロールとナビゲーションツールバーコントロールがデザイナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-236">By default, a new DataGridView (**categoryDataGridView**) and Navigation toolbar controls are added to the designer.</span></span> <span data-ttu-id="e1d5d-237">これらのコントロールは、作成された BindingSource (**カテゴリ bindingsource**) コンポーネントとバインドナビゲーター (**カテゴリ bindingnavigator**) コンポーネントにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-237">These controls are bound to the BindingSource (**categoryBindingSource**) and Binding Navigator (**categoryBindingNavigator**) components that are created as well.</span></span>
-   <span data-ttu-id="e1d5d-238">**カテゴリ datagridview**の列を編集します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-238">Edit the columns on the **categoryDataGridView**.</span></span> <span data-ttu-id="e1d5d-239">**CategoryId**列を読み取り専用に設定します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-239">We want to set the **CategoryId** column to read-only.</span></span> <span data-ttu-id="e1d5d-240">**CategoryId**プロパティの値は、データを保存した後にデータベースによって生成されます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-240">The value for the **CategoryId** property is generated by the database after we save the data.</span></span>
    -   <span data-ttu-id="e1d5d-241">DataGridView コントロールを右クリックし、[列の編集...] を選択します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-241">Right-click the DataGridView control and select Edit Columns…</span></span>
    -   <span data-ttu-id="e1d5d-242">CategoryId 列を選択し、ReadOnly を True に設定します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-242">Select the CategoryId column and set ReadOnly to True</span></span>
    -   <span data-ttu-id="e1d5d-243">[OK] を押します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-243">Press OK</span></span>
-   <span data-ttu-id="e1d5d-244">カテゴリデータソースの下にある [Products] を選択し、フォームにドラッグします。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-244">Select Products from under the Category data source and drag it on the form.</span></span> <span data-ttu-id="e1d5d-245">ProductDataGridView と Productdatagridview がフォームに追加されます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-245">The productDataGridView and productBindingSource are added to the form.</span></span>
-   <span data-ttu-id="e1d5d-246">ProductDataGridView の列を編集します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-246">Edit the columns on the productDataGridView.</span></span> <span data-ttu-id="e1d5d-247">CategoryId 列と Category 列を非表示にし、ProductId を読み取り専用に設定します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-247">We want to hide the CategoryId and Category columns and set ProductId to read-only.</span></span> <span data-ttu-id="e1d5d-248">ProductId プロパティの値は、データを保存した後にデータベースによって生成されます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-248">The value for the ProductId property is generated by the database after we save the data.</span></span>
    -   <span data-ttu-id="e1d5d-249">DataGridView コントロールを右クリックし、 **[列の編集...]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-249">Right-click the DataGridView control and select **Edit Columns...**.</span></span>
    -   <span data-ttu-id="e1d5d-250">**ProductId**列を選択し、 **ReadOnly**を**True**に設定します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-250">Select the **ProductId** column and set **ReadOnly** to **True**.</span></span>
    -   <span data-ttu-id="e1d5d-251">**CategoryId**列を選択し、 **[削除]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-251">Select the **CategoryId** column and press the **Remove** button.</span></span> <span data-ttu-id="e1d5d-252">**Category**列でも同じ操作を行います。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-252">Do the same with the **Category** column.</span></span>
    -   <span data-ttu-id="e1d5d-253">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-253">Press **OK**.</span></span>

    <span data-ttu-id="e1d5d-254">ここまでで、DataGridView コントロールはデザイナーの BindingSource コンポーネントに関連付けられていました。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-254">So far, we associated our DataGridView controls with BindingSource components in the designer.</span></span> <span data-ttu-id="e1d5d-255">次のセクションでは、コードを分離コードに追加して、現在 DbContext によって追跡されているエンティティのコレクションにカテゴリ Bindingsource を設定します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-255">In the next section we will add code to the code behind to set categoryBindingSource.DataSource to the collection of entities that are currently tracked by DbContext.</span></span> <span data-ttu-id="e1d5d-256">カテゴリの下から製品をドラッグアンドドロップした場合、WinForms プロパティを Category Bindingsource に設定し、Products に製品を設定することが処理されました。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-256">When we dragged-and-dropped Products from under the Category, the WinForms took care of setting up the productsBindingSource.DataSource property to categoryBindingSource and productsBindingSource.DataMember property to Products.</span></span> <span data-ttu-id="e1d5d-257">このバインドにより、現在選択されているカテゴリに属する製品だけが productDataGridView に表示されます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-257">Because of this binding, only the products that belong to the currently selected Category will be displayed in the productDataGridView.</span></span>
-   <span data-ttu-id="e1d5d-258">ナビゲーションツールバーの **[保存]** ボタンを有効にするには、マウスの右ボタンをクリックし、 **[有効]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-258">Enable the **Save** button on the Navigation toolbar by clicking the right mouse button and selecting **Enabled**.</span></span>

    ![フォーム1デザイナー](~/ef6/media/form1-designer.png)

-   <span data-ttu-id="e1d5d-260">[保存] ボタンのイベントハンドラーを、ボタンをダブルクリックして追加します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-260">Add the event handler for the save button by double-clicking on the button.</span></span> <span data-ttu-id="e1d5d-261">これにより、イベントハンドラーが追加され、フォームの分離コードに移動します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-261">This will add the event handler and bring you to the code behind for the form.</span></span> <span data-ttu-id="e1d5d-262">次のセクションでは、 **categoryBindingNavigatorSaveItem\_click**イベントハンドラーのコードが追加されます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-262">The code for the **categoryBindingNavigatorSaveItem\_Click** event handler will be added in the next section.</span></span>

## <a name="add-the-code-that-handles-data-interaction"></a><span data-ttu-id="e1d5d-263">データの相互作用を処理するコードを追加する</span><span class="sxs-lookup"><span data-stu-id="e1d5d-263">Add the Code that Handles Data Interaction</span></span>

<span data-ttu-id="e1d5d-264">次に、ProductContext を使用してデータアクセスを実行するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-264">We'll now add the code to use the ProductContext to perform data access.</span></span> <span data-ttu-id="e1d5d-265">次に示すように、メインフォームウィンドウのコードを更新します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-265">Update the code for the main form window as shown below.</span></span>

<span data-ttu-id="e1d5d-266">このコードは、ProductContext の実行時間の長いインスタンスを宣言します。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-266">The code declares a long-running instance of ProductContext.</span></span> <span data-ttu-id="e1d5d-267">ProductContext オブジェクトは、データをクエリしてデータベースに保存するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-267">The ProductContext object is used to query and save data to the database.</span></span> <span data-ttu-id="e1d5d-268">その後、ProductContext インスタンスの Dispose () メソッドが、オーバーライドされた OnClosing メソッドから呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-268">The Dispose() method on the ProductContext instance is then called from the overridden OnClosing method.</span></span> <span data-ttu-id="e1d5d-269">コードのコメントには、コードの動作についての詳細が記載されています。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-269">The code comments provide details about what the code does.</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.ComponentModel;
    using System.Data;
    using System.Drawing;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Windows.Forms;
    using System.Data.Entity;

    namespace WinFormswithEFSample
    {
        public partial class Form1 : Form
        {
            ProductContext _context;
            public Form1()
            {
                InitializeComponent();
            }

            protected override void OnLoad(EventArgs e)
            {
                base.OnLoad(e);
                _context = new ProductContext();

                // Call the Load method to get the data for the given DbSet
                // from the database.
                // The data is materialized as entities. The entities are managed by
                // the DbContext instance.
                _context.Categories.Load();

                // Bind the categoryBindingSource.DataSource to
                // all the Unchanged, Modified and Added Category objects that
                // are currently tracked by the DbContext.
                // Note that we need to call ToBindingList() on the
                // ObservableCollection<TEntity> returned by
                // the DbSet.Local property to get the BindingList<T>
                // in order to facilitate two-way binding in WinForms.
                this.categoryBindingSource.DataSource =
                    _context.Categories.Local.ToBindingList();
            }

            private void categoryBindingNavigatorSaveItem_Click(object sender, EventArgs e)
            {
                this.Validate();

                // Currently, the Entity Framework doesn’t mark the entities
                // that are removed from a navigation property (in our example the Products)
                // as deleted in the context.
                // The following code uses LINQ to Objects against the Local collection
                // to find all products and marks any that do not have
                // a Category reference as deleted.
                // The ToList call is required because otherwise
                // the collection will be modified
                // by the Remove call while it is being enumerated.
                // In most other situations you can do LINQ to Objects directly
                // against the Local property without using ToList first.
                foreach (var product in _context.Products.Local.ToList())
                {
                    if (product.Category == null)
                    {
                        _context.Products.Remove(product);
                    }
                }

                // Save the changes to the database.
                this._context.SaveChanges();

                // Refresh the controls to show the values         
                // that were generated by the database.
                this.categoryDataGridView.Refresh();
                this.productsDataGridView.Refresh();
            }

            protected override void OnClosing(CancelEventArgs e)
            {
                base.OnClosing(e);
                this._context.Dispose();
            }
        }
    }
```

## <a name="test-the-windows-forms-application"></a><span data-ttu-id="e1d5d-270">Windows フォームアプリケーションのテスト</span><span class="sxs-lookup"><span data-stu-id="e1d5d-270">Test the Windows Forms Application</span></span>

-   <span data-ttu-id="e1d5d-271">アプリケーションをコンパイルして実行すると、機能をテストできます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-271">Compile and run the application and you can test out the functionality.</span></span>

    ![保存前のフォーム1](~/ef6/media/form1beforesave.png)

-   <span data-ttu-id="e1d5d-273">保存すると、ストアによって生成されたキーが画面に表示されます。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-273">After saving the store generated keys are shown on the screen.</span></span>

    ![保存後のフォーム1](~/ef6/media/form1aftersave.png)

-   <span data-ttu-id="e1d5d-275">Code First を使用した場合は、 **Winフォーム Withefsample. ProductContext**データベースが作成されていることもわかります。</span><span class="sxs-lookup"><span data-stu-id="e1d5d-275">If you used Code First, then you will also see that a **WinFormswithEFSample.ProductContext** database is created for you.</span></span>

    ![サーバーオブジェクトエクスプローラー](~/ef6/media/serverobjexplorer.png)
