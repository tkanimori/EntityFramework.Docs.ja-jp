---
title: WinForms - EF6 とのデータ バインド
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 80fc5062-2f1c-4dbd-ab6e-b99496784b36
caps.latest.revision: 3
ms.openlocfilehash: b17bc91fd7d665f6d75bf5f1e5798ddd16aa345d
ms.sourcegitcommit: 45494121254ad4fdcec613d1dd22d850068d6f39
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2018
ms.locfileid: "39122540"
---
# <a name="databinding-with-winforms"></a><span data-ttu-id="b765e-102">WinForms とのデータ バインド</span><span class="sxs-lookup"><span data-stu-id="b765e-102">Databinding with WinForms</span></span>
<span data-ttu-id="b765e-103">このステップ バイ ステップ チュートリアルでは、POCO 型を「マスター/詳細」の形式で、ウィンドウ フォーム (WinForms) コントロールにバインドする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="b765e-103">This step-by-step walkthrough shows how to bind POCO types to Window Forms (WinForms) controls in a “master-detail" form.</span></span> <span data-ttu-id="b765e-104">アプリケーションでは、Entity Framework を使用して、データベースからデータをオブジェクトに設定、変更の追跡、およびデータベースにデータを保持します。</span><span class="sxs-lookup"><span data-stu-id="b765e-104">The application uses Entity Framework to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="b765e-105">モデルが一対多のリレーションシップに参加している 2 つの型を定義します。 カテゴリ (プリンシパル\\マスター) と製品 (依存\\詳細)。</span><span class="sxs-lookup"><span data-stu-id="b765e-105">The model defines two types that participate in one-to-many relationship: Category (principal\\master) and Product (dependent\\detail).</span></span> <span data-ttu-id="b765e-106">次に、Visual Studio ツールは、WinForms コントロールのモデルで定義された型のバインドに使用されます。</span><span class="sxs-lookup"><span data-stu-id="b765e-106">Then, the Visual Studio tools are used to bind the types defined in the model to the WinForms controls.</span></span> <span data-ttu-id="b765e-107">WinForms データ バインディング フレームワークにより、関連するオブジェクト間のナビゲーション: により、対応する子データで更新する詳細ビューがマスター ビューの行を選択します。</span><span class="sxs-lookup"><span data-stu-id="b765e-107">The WinForms data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="b765e-108">スクリーン ショットとこのチュートリアルで示した各コードは、Visual Studio 2013 から取得されますが、このチュートリアルでは、Visual Studio 2012 または Visual Studio 2010 を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="b765e-108">The screen shots and code listings in this walkthrough are taken from Visual Studio 2013 but you can complete this walkthrough with Visual Studio 2012 or Visual Studio 2010.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="b765e-109">前提条件</span><span class="sxs-lookup"><span data-stu-id="b765e-109">Pre-Requisites</span></span>

<span data-ttu-id="b765e-110">Visual Studio 2013 を持っている必要があります、このチュートリアルを完了する、Visual Studio 2012 または Visual Studio 2010 がインストールされています。</span><span class="sxs-lookup"><span data-stu-id="b765e-110">You need to have Visual Studio 2013, Visual Studio 2012 or Visual Studio 2010 installed to complete this walkthrough.</span></span>

<span data-ttu-id="b765e-111">Visual Studio 2010 を使用している場合は、NuGet をインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b765e-111">If you are using Visual Studio 2010, you also have to install NuGet.</span></span> <span data-ttu-id="b765e-112">詳細については、次を参照してください。[をインストールする NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)します。</span><span class="sxs-lookup"><span data-stu-id="b765e-112">For more information, see [Installing NuGet](http://docs.nuget.org/docs/start-here/installing-nuget).</span></span>

## <a name="create-the-application"></a><span data-ttu-id="b765e-113">アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="b765e-113">Create the Application</span></span>

-   <span data-ttu-id="b765e-114">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="b765e-114">Open Visual Studio</span></span>
-   <span data-ttu-id="b765e-115">**ファイル -&gt;新機能 -&gt;プロジェクト.**</span><span class="sxs-lookup"><span data-stu-id="b765e-115">**File -&gt; New -&gt; Project….**</span></span>
-   <span data-ttu-id="b765e-116">選択**Windows**左側のウィンドウで、 **Windows FormsApplication**右側のウィンドウで</span><span class="sxs-lookup"><span data-stu-id="b765e-116">Select **Windows** in the left pane and **Windows FormsApplication** in the right pane</span></span>
-   <span data-ttu-id="b765e-117">入力**WinFormswithEFSample**名として</span><span class="sxs-lookup"><span data-stu-id="b765e-117">Enter **WinFormswithEFSample** as the name</span></span>
-   <span data-ttu-id="b765e-118">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b765e-118">Select **OK**</span></span>

## <a name="install-the-entity-framework-nuget-package"></a><span data-ttu-id="b765e-119">Entity Framework NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="b765e-119">Install the Entity Framework NuGet package</span></span>

-   <span data-ttu-id="b765e-120">ソリューション エクスプ ローラーを右クリックし、 **WinFormswithEFSample**プロジェクト</span><span class="sxs-lookup"><span data-stu-id="b765e-120">In Solution Explorer, right-click on the **WinFormswithEFSample** project</span></span>
-   <span data-ttu-id="b765e-121">選択**NuGet パッケージを管理しています.**</span><span class="sxs-lookup"><span data-stu-id="b765e-121">Select **Manage NuGet Packages…**</span></span>
-   <span data-ttu-id="b765e-122">NuGet パッケージの管理 ダイアログ ボックスで、、**オンライン** タブで選択し、 **EntityFramework**パッケージ</span><span class="sxs-lookup"><span data-stu-id="b765e-122">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
-   <span data-ttu-id="b765e-123">クリックして**インストール**</span><span class="sxs-lookup"><span data-stu-id="b765e-123">Click **Install**</span></span>  
    > [!NOTE]
    > <span data-ttu-id="b765e-124">EntityFramework アセンブリだけでなく System.ComponentModel.DataAnnotations への参照も追加されます。</span><span class="sxs-lookup"><span data-stu-id="b765e-124">In addition to the EntityFramework assembly a reference to System.ComponentModel.DataAnnotations is also added.</span></span> <span data-ttu-id="b765e-125">System.Data.Entity への参照をプロジェクトには場合、は、EntityFramework パッケージがインストールされている場合、削除するされます。</span><span class="sxs-lookup"><span data-stu-id="b765e-125">If the project has a reference to System.Data.Entity, then it will be removed when the EntityFramework package is installed.</span></span> <span data-ttu-id="b765e-126">System.Data.Entity アセンブリは Entity Framework 6 アプリケーションは使用されません。</span><span class="sxs-lookup"><span data-stu-id="b765e-126">The System.Data.Entity assembly is no longer used for Entity Framework 6 applications.</span></span>

## <a name="implementing-ilistsource-for-collections"></a><span data-ttu-id="b765e-127">コレクションの IListSource の実装</span><span class="sxs-lookup"><span data-stu-id="b765e-127">Implementing IListSource for Collections</span></span>

<span data-ttu-id="b765e-128">コレクションのプロパティは、双方向データ バインド Windows フォームを使用する場合の並べ替えを有効にする IListSource インターフェイスを実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b765e-128">Collection properties must implement the IListSource interface to enable two-way data binding with sorting when using Windows Forms.</span></span> <span data-ttu-id="b765e-129">IListSource の機能を追加する ObservableCollection を拡張しようとしてこれを行うには。</span><span class="sxs-lookup"><span data-stu-id="b765e-129">To do this we are going to extend ObservableCollection to add IListSource functionality.</span></span>

-   <span data-ttu-id="b765e-130">追加、 **ObservableListSource**クラスをプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="b765e-130">Add an **ObservableListSource** class to the project:</span></span>
    -   <span data-ttu-id="b765e-131">プロジェクト名を右クリックします。</span><span class="sxs-lookup"><span data-stu-id="b765e-131">Right-click on the project name</span></span>
    -   <span data-ttu-id="b765e-132">選択**追加 -&gt;新しい項目**</span><span class="sxs-lookup"><span data-stu-id="b765e-132">Select **Add -&gt; New Item**</span></span>
    -   <span data-ttu-id="b765e-133">選択**クラス**入力**ObservableListSource**クラス名</span><span class="sxs-lookup"><span data-stu-id="b765e-133">Select **Class** and enter **ObservableListSource** for the class name</span></span>
-   <span data-ttu-id="b765e-134">既定では、次のコードで生成されたコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b765e-134">Replace the code generated by default with the following code:</span></span>

<span data-ttu-id="b765e-135">*このクラスには、双方向データ バインドと並べ替えができるようにします。クラスは ObservableCollection から派生&lt;T&gt; IListSource の明示的な実装を追加します。IListSource の GetList() メソッドは、同期、ObservableCollection を維持する IBindingList 実装を返す実装されます。ToBindingList によって生成された IBindingList 実装では、並べ替えをサポートします。ToBindingList の拡張機能メソッドは、EntityFramework アセンブリで定義されます。*</span><span class="sxs-lookup"><span data-stu-id="b765e-135">*This class enables two-way data binding as well as sorting. The class derives from ObservableCollection&lt;T&gt; and adds an explicit implementation of IListSource. The GetList() method of IListSource is implemented to return an IBindingList implementation that stays in sync with the ObservableCollection. The IBindingList implementation generated by ToBindingList supports sorting. The ToBindingList extention method is defined in the EntityFramework assembly.*</span></span>

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
                return _bindingList  (_bindingList = this.ToBindingList());
            }
        }
    }
```

## <a name="define-a-model"></a><span data-ttu-id="b765e-136">モデルを定義します。</span><span class="sxs-lookup"><span data-stu-id="b765e-136">Define a Model</span></span>

<span data-ttu-id="b765e-137">できます、このチュートリアルでは、Code First または EF Designer を使用してモデルを実装するために選択しました。</span><span class="sxs-lookup"><span data-stu-id="b765e-137">In this walkthrough you can chose to implement a model using Code First or the EF Designer.</span></span> <span data-ttu-id="b765e-138">2 つのセクションでは、次のいずれかを完了します。</span><span class="sxs-lookup"><span data-stu-id="b765e-138">Complete one of the two following sections.</span></span>

### <a name="option-1-define-a-model-using-code-first"></a><span data-ttu-id="b765e-139">オプション 1: Code First を使用してモデルを定義します。</span><span class="sxs-lookup"><span data-stu-id="b765e-139">Option 1: Define a Model using Code First</span></span>

<span data-ttu-id="b765e-140">このセクションでは、モデルおよび関連する Code First を使用してデータベースを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="b765e-140">This section shows how to create a model and its associated database using Code First.</span></span> <span data-ttu-id="b765e-141">次のセクションにスキップ (**オプション 2: Database First を使用してモデルを定義)** エンジニア リング EF designer を使用してデータベースからモデルを使用する場合は、データベースの最初を逆にします。</span><span class="sxs-lookup"><span data-stu-id="b765e-141">Skip to the next section (**Option 2: Define a model using Database First)** if you would rather use Database First to reverse engineer your model from a database using the EF designer</span></span>

<span data-ttu-id="b765e-142">Code First の開発を使用する場合に、(ドメイン) の概念モデルを定義する .NET Framework クラスを記述することで、通常で開始します。</span><span class="sxs-lookup"><span data-stu-id="b765e-142">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span>

-   <span data-ttu-id="b765e-143">新しい追加**製品**プロジェクトにクラス</span><span class="sxs-lookup"><span data-stu-id="b765e-143">Add a new **Product** class to project</span></span>
-   <span data-ttu-id="b765e-144">既定では、次のコードで生成されたコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b765e-144">Replace the code generated by default with the following code:</span></span>

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

-   <span data-ttu-id="b765e-145">追加、**カテゴリ**クラスをプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="b765e-145">Add a **Category** class to the project.</span></span>
-   <span data-ttu-id="b765e-146">既定では、次のコードで生成されたコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b765e-146">Replace the code generated by default with the following code:</span></span>

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

<span data-ttu-id="b765e-147">エンティティを定義するだけでなくから派生したクラスを定義する必要があります。 **DbContext**公開**DbSet&lt;TEntity&gt;** プロパティ。</span><span class="sxs-lookup"><span data-stu-id="b765e-147">In addition to defining entities, you need to define a class that derives from **DbContext** and exposes **DbSet&lt;TEntity&gt;** properties.</span></span> <span data-ttu-id="b765e-148">**DbSet**プロパティは、モデルに追加する型も把握してコンテキストを使用します。</span><span class="sxs-lookup"><span data-stu-id="b765e-148">The **DbSet** properties let the context know which types you want to include in the model.</span></span> <span data-ttu-id="b765e-149">**DbContext**と**DbSet**型 EntityFramework アセンブリで定義されます。</span><span class="sxs-lookup"><span data-stu-id="b765e-149">The **DbContext** and **DbSet** types are defined in the EntityFramework assembly.</span></span>

<span data-ttu-id="b765e-150">DbContext 派生型のインスタンスがデータベースからデータを使用してオブジェクトの設定が含まれています。 ランタイム処理中に、エンティティ オブジェクトを管理、追跡、および永続化するデータをデータベースに変更します。</span><span class="sxs-lookup"><span data-stu-id="b765e-150">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

-   <span data-ttu-id="b765e-151">新しい追加**ProductContext**クラスをプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="b765e-151">Add a new **ProductContext** class to the project.</span></span>
-   <span data-ttu-id="b765e-152">既定では、次のコードで生成されたコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b765e-152">Replace the code generated by default with the following code:</span></span>

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

<span data-ttu-id="b765e-153">プロジェクトをコンパイルします。</span><span class="sxs-lookup"><span data-stu-id="b765e-153">Compile the project.</span></span>

### <a name="option-2-define-a-model-using-database-first"></a><span data-ttu-id="b765e-154">オプション 2: Database First を使用してモデルを定義します。</span><span class="sxs-lookup"><span data-stu-id="b765e-154">Option 2: Define a model using Database First</span></span>

<span data-ttu-id="b765e-155">このセクションでは、EF designer を使用してデータベースからモデルをリバース エンジニア リング Database First を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="b765e-155">This section shows how to use Database First to reverse engineer your model from a database using the EF designer.</span></span> <span data-ttu-id="b765e-156">前のセクションを完了している場合 (**オプション 1: Code First を使用してモデルを定義)**、このセクションをスキップし、すぐに進んで、 **Lazy Loading**セクション。</span><span class="sxs-lookup"><span data-stu-id="b765e-156">If you completed the previous section (**Option 1: Define a model using Code First)**, then skip this section and go straight to the **Lazy Loading** section.</span></span>

#### <a name="create-an-existing-database"></a><span data-ttu-id="b765e-157">既存のデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="b765e-157">Create an Existing Database</span></span>

<span data-ttu-id="b765e-158">通常それは既に作成されて、既存のデータベースを対象としているときに、このチュートリアルではアクセスするデータベースを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b765e-158">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="b765e-159">Visual Studio と共にインストールされているデータベース サーバーは、インストールした Visual Studio のバージョンによって異なります。</span><span class="sxs-lookup"><span data-stu-id="b765e-159">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="b765e-160">Visual Studio 2010 を使用している場合は、SQL Express データベースを作成するがします。</span><span class="sxs-lookup"><span data-stu-id="b765e-160">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="b765e-161">Visual Studio 2012 を使用しているかどうかは、作成する、 [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx)データベース。</span><span class="sxs-lookup"><span data-stu-id="b765e-161">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) database.</span></span>

<span data-ttu-id="b765e-162">データベースを生成してみましょう。</span><span class="sxs-lookup"><span data-stu-id="b765e-162">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="b765e-163">**ビュー -&gt;サーバー エクスプ ローラー**</span><span class="sxs-lookup"><span data-stu-id="b765e-163">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="b765e-164">右クリックして**データ接続 -&gt;接続の追加.**</span><span class="sxs-lookup"><span data-stu-id="b765e-164">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="b765e-165">場合、まだデータベースに接続して、サーバー エクスプ ローラーから前に、データ ソースとして Microsoft SQL Server を選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b765e-165">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![ChangeDataSource](~/ef6/media/changedatasource.png)

-   <span data-ttu-id="b765e-167">LocalDB または SQL Express をインストールしたものに応じてのいずれかに接続し、入力**製品**データベース名として</span><span class="sxs-lookup"><span data-stu-id="b765e-167">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **Products** as the database name</span></span>

    ![AddConnectionLocalDB](~/ef6/media/addconnectionlocaldb.png)

    ![AddConnectionExpress](~/ef6/media/addconnectionexpress.png)

-   <span data-ttu-id="b765e-170">選択**OK**かどうかは、新しいデータベースを作成するように要求がある **[はい]**</span><span class="sxs-lookup"><span data-stu-id="b765e-170">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![CreateDatabase](~/ef6/media/createdatabase.png)

-   <span data-ttu-id="b765e-172">新しいデータベースがサーバー エクスプ ローラーに表示されますを右クリックして選択**新しいクエリ**</span><span class="sxs-lookup"><span data-stu-id="b765e-172">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="b765e-173">新しいクエリに、次の SQL をコピーし、クエリを選択します右クリックし、 **Execute**</span><span class="sxs-lookup"><span data-stu-id="b765e-173">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

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

#### <a name="reverse-engineer-model"></a><span data-ttu-id="b765e-174">リバース エンジニア リング モデル</span><span class="sxs-lookup"><span data-stu-id="b765e-174">Reverse Engineer Model</span></span>

<span data-ttu-id="b765e-175">Visual Studio の一部として含まれている Entity Framework デザイナーを使用してモデルを作成することになります。</span><span class="sxs-lookup"><span data-stu-id="b765e-175">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="b765e-176">**プロジェクト -&gt;新しい項目を追加しています.**</span><span class="sxs-lookup"><span data-stu-id="b765e-176">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="b765e-177">選択**データ**左側のメニューをクリックし**ADO.NET Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="b765e-177">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="b765e-178">入力**ProductModel**名をクリックします**OK**</span><span class="sxs-lookup"><span data-stu-id="b765e-178">Enter **ProductModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="b765e-179">これにより、起動、 **Entity Data Model ウィザード**</span><span class="sxs-lookup"><span data-stu-id="b765e-179">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="b765e-180">選択**データベースから生成**クリック **[次へ]**</span><span class="sxs-lookup"><span data-stu-id="b765e-180">Select **Generate from Database** and click **Next**</span></span>

    ![ChooseModelContents](~/ef6/media/choosemodelcontents.png)

-   <span data-ttu-id="b765e-182">最初のセクションで作成したデータベース接続を選択して、入力**ProductContext**をクリックして、接続文字列の名前として **[次へ]**</span><span class="sxs-lookup"><span data-stu-id="b765e-182">Select the connection to the database you created in the first section, enter **ProductContext** as the name of the connection string and click **Next**</span></span>

    ![ChooseYourConnection](~/ef6/media/chooseyourconnection.png)

-   <span data-ttu-id="b765e-184">すべてのテーブルをインポートし、[完了] をクリックします。 'テーブル' の横にあるチェック ボックスをクリックします。</span><span class="sxs-lookup"><span data-stu-id="b765e-184">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![ChooseYourObjects](~/ef6/media/chooseyourobjects.png)

<span data-ttu-id="b765e-186">リバース エンジニア リング プロセスが完了すると、新しいモデルがプロジェクトに追加しを開いて、Entity Framework デザイナーで表示することです。</span><span class="sxs-lookup"><span data-stu-id="b765e-186">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="b765e-187">App.config ファイルが、データベースの接続詳細を含むプロジェクトにも追加されました。</span><span class="sxs-lookup"><span data-stu-id="b765e-187">An App.config file has also been added to your project with the connection details for the database.</span></span>

#### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="b765e-188">Visual Studio 2010 で追加の手順</span><span class="sxs-lookup"><span data-stu-id="b765e-188">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="b765e-189">Visual Studio 2010 で作業している場合は、EF6 コード生成を使用する EF designer を更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b765e-189">If you are working in Visual Studio 2010 then you will need to update the EF designer to use EF6 code generation.</span></span>

-   <span data-ttu-id="b765e-190">EF Designer でのモデルの空いている場所を右クリックして**コード生成項目の追加.**</span><span class="sxs-lookup"><span data-stu-id="b765e-190">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="b765e-191">選択**オンライン テンプレート**、左側のメニューと検索から**DbContext**</span><span class="sxs-lookup"><span data-stu-id="b765e-191">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="b765e-192">選択、 **EF 6.x C 用 DbContext ジェネレーター\#、** 入力**ProductsModel**名として追加 をクリック</span><span class="sxs-lookup"><span data-stu-id="b765e-192">Select the **EF 6.x DbContext Generator for C\#,** enter **ProductsModel** as the name and click Add</span></span>

#### <a name="updating-code-generation-for-data-binding"></a><span data-ttu-id="b765e-193">データ バインディングの更新のコード生成</span><span class="sxs-lookup"><span data-stu-id="b765e-193">Updating code generation for data binding</span></span>

<span data-ttu-id="b765e-194">EF では、T4 テンプレートを使用して、モデルからコードを生成します。</span><span class="sxs-lookup"><span data-stu-id="b765e-194">EF generates code from your model using T4 templates.</span></span> <span data-ttu-id="b765e-195">Visual Studio に付属のか、Visual Studio ギャラリーからダウンロードしたテンプレートは、一般的な用途に使用されるもの。</span><span class="sxs-lookup"><span data-stu-id="b765e-195">The templates shipped with Visual Studio or downloaded from the Visual Studio gallery are intended for general purpose use.</span></span> <span data-ttu-id="b765e-196">つまり、これらのテンプレートから生成されたエンティティがある単純な ICollection&lt;T&gt;プロパティ。</span><span class="sxs-lookup"><span data-stu-id="b765e-196">This means that the entities generated from these templates have simple ICollection&lt;T&gt; properties.</span></span> <span data-ttu-id="b765e-197">ただし、データ バインディングの実行時は IListSource を実装するコレクションのプロパティを用意することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="b765e-197">However, when doing data binding it is desirable to have collection properties that implement IListSource.</span></span> <span data-ttu-id="b765e-198">これは、ため、上記の ObservableListSource クラスを作成したとするテンプレートを変更しようとして今すぐこのクラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="b765e-198">This is why we created the ObservableListSource class above and we are now going to modify the templates to make use of this class.</span></span>

-   <span data-ttu-id="b765e-199">開く、**ソリューション エクスプ ローラー**を見つけて**ProductModel.edmx**ファイル</span><span class="sxs-lookup"><span data-stu-id="b765e-199">Open the **Solution Explorer** and find **ProductModel.edmx** file</span></span>
-   <span data-ttu-id="b765e-200">検索、 **ProductModel.tt** ProductModel.edmx ファイルの下で入れ子にはファイル</span><span class="sxs-lookup"><span data-stu-id="b765e-200">Find the **ProductModel.tt** file which will be nested under the ProductModel.edmx file</span></span>

    ![ProductModelTemplate](~/ef6/media/productmodeltemplate.png)

-   <span data-ttu-id="b765e-202">Visual Studio エディターで開く ProductModel.tt ファイルをダブルクリックします</span><span class="sxs-lookup"><span data-stu-id="b765e-202">Double-click on the ProductModel.tt file to open it in the Visual Studio editor</span></span>
-   <span data-ttu-id="b765e-203">検索し、置換の 2 つの出現回数"**ICollection**"with"**ObservableListSource**"。</span><span class="sxs-lookup"><span data-stu-id="b765e-203">Find and replace the two occurrences of “**ICollection**” with “**ObservableListSource**”.</span></span> <span data-ttu-id="b765e-204">これらは、296 と 484 を約行にあります。</span><span class="sxs-lookup"><span data-stu-id="b765e-204">These are located at approximately lines 296 and 484.</span></span>
-   <span data-ttu-id="b765e-205">検索し、置換の最初に見つかった"**HashSet**"with"**ObservableListSource**"。</span><span class="sxs-lookup"><span data-stu-id="b765e-205">Find and replace the first occurrence of “**HashSet**” with “**ObservableListSource**”.</span></span> <span data-ttu-id="b765e-206">この状況の発生は、約 50 行に配置されます。</span><span class="sxs-lookup"><span data-stu-id="b765e-206">This occurrence is located at approximately line 50.</span></span> <span data-ttu-id="b765e-207">**しない**HashSet は後で、コードの 2 番目に出現を置換します。</span><span class="sxs-lookup"><span data-stu-id="b765e-207">**Do not** replace the second occurrence of HashSet found later in the code.</span></span>
-   <span data-ttu-id="b765e-208">ProductModel.tt ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="b765e-208">Save the ProductModel.tt file.</span></span> <span data-ttu-id="b765e-209">これは、再生成するエンティティのコードで発生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b765e-209">This should cause the code for entities to be regenerated.</span></span> <span data-ttu-id="b765e-210">コードは自動的に再生成しない場合は、ProductModel.tt を右クリックし、「カスタム ツールの実行」を選択します。</span><span class="sxs-lookup"><span data-stu-id="b765e-210">If the code does not regenerate automatically, then right click on ProductModel.tt and choose “Run Custom Tool”.</span></span>

<span data-ttu-id="b765e-211">かどうか (これは ProductModel.tt 下で入れ子になった) Category.cs ファイルを開くようになりましたし、製品のコレクションが、型を持つことを確認する必要があります**ObservableListSource&lt;製品&gt;** します。</span><span class="sxs-lookup"><span data-stu-id="b765e-211">If you now open the Category.cs file (which is nested under ProductModel.tt) then you should see that the Products collection has the type **ObservableListSource&lt;Product&gt;**.</span></span>

<span data-ttu-id="b765e-212">プロジェクトをコンパイルします。</span><span class="sxs-lookup"><span data-stu-id="b765e-212">Compile the project.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="b765e-213">遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="b765e-213">Lazy Loading</span></span>

<span data-ttu-id="b765e-214">**製品**プロパティを**カテゴリ**クラスと**カテゴリ**プロパティを**製品**クラスは、ナビゲーション プロパティ。</span><span class="sxs-lookup"><span data-stu-id="b765e-214">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="b765e-215">Entity Framework では、ナビゲーション プロパティは、2 つのエンティティ型間のリレーションシップを移動する方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="b765e-215">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="b765e-216">EF では、関連エンティティを読み込む、データベースから自動的に初めてナビゲーション プロパティにアクセスするの選ぶことができます。</span><span class="sxs-lookup"><span data-stu-id="b765e-216">EF gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="b765e-217">この種類の読み込み (遅延読み込みと呼ばれます) にするには、初めての各ナビゲーション プロパティにアクセスする別のクエリが実行されること、データベースに対して、内容が既にコンテキストでない場合あります。</span><span class="sxs-lookup"><span data-stu-id="b765e-217">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="b765e-218">POCO エンティティ型を使用する場合、EF は実行時にプロキシの派生型のインスタンスを作成し、読み込み用のフックを追加するクラスの仮想プロパティをオーバーライドして遅延読み込みを実現します。</span><span class="sxs-lookup"><span data-stu-id="b765e-218">When using POCO entity types, EF achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="b765e-219">関連オブジェクトの遅延読み込みを取得する必要がありますを宣言するナビゲーション プロパティの getter として**パブリック**と**仮想**(**Overridable** Visual Basic で)、およびするクラスがすることはできません**シール**(**NotOverridable** Visual Basic で)。</span><span class="sxs-lookup"><span data-stu-id="b765e-219">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and you class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="b765e-220">データベースを使用して最初のナビゲーション プロパティは自動的遅延読み込みを有効にするための仮想になります。</span><span class="sxs-lookup"><span data-stu-id="b765e-220">When using Database First navigation properties are automatically made virtual to enable lazy loading.</span></span> <span data-ttu-id="b765e-221">コードの最初のセクションで同じ理由から仮想ナビゲーション プロパティを作成することにしました</span><span class="sxs-lookup"><span data-stu-id="b765e-221">In the Code First section we chose to make the navigation properties virtual for the same reason</span></span>

## <a name="bind-object-to-controls"></a><span data-ttu-id="b765e-222">オブジェクトをコントロールにバインドします。</span><span class="sxs-lookup"><span data-stu-id="b765e-222">Bind Object to Controls</span></span>

<span data-ttu-id="b765e-223">この WinForms アプリケーションのデータ ソースとしてモデルに定義されているクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="b765e-223">Add the classes that are defined in the model as data sources for this WinForms application.</span></span>

-   <span data-ttu-id="b765e-224">メイン メニューで、次のように選択します**プロジェクト -&gt;新しいデータ ソースを追加しています。**</span><span class="sxs-lookup"><span data-stu-id="b765e-224">From the main menu, select **Project -&gt; Add New Data Source …**</span></span>
    <span data-ttu-id="b765e-225">(Visual Studio 2010 では、選択する必要があります**データ -&gt;新しいデータ ソースの追加...**)</span><span class="sxs-lookup"><span data-stu-id="b765e-225">(in Visual Studio 2010, you need to select **Data -&gt; Add New Data Source…**)</span></span>
-   <span data-ttu-id="b765e-226">データ ソースの種類の選択、次のように選択します**オブジェクト**クリック**次へ。**</span><span class="sxs-lookup"><span data-stu-id="b765e-226">In the Choose a Data Source Type window, select **Object** and click **Next**</span></span>
-   <span data-ttu-id="b765e-227">選択、データ オブジェクト ダイアログ ボックスで、展開、 **WinFormswithEFSample** 2 つの時刻と選択**カテゴリ**製品の使用をいたしますので、製品のデータ ソースを選択する必要はありませんカテゴリのデータ ソースのプロパティです。</span><span class="sxs-lookup"><span data-stu-id="b765e-227">In the Select the Data Objects dialog, unfold the **WinFormswithEFSample** two times and select **Category** There is no need to select the Product data source, because we will get to it through the Product’s property on the Category data source.</span></span>

    ![DataSource](~/ef6/media/datasource.png)

-   <span data-ttu-id="b765e-229">クリックして**完了します**。
    *データ ソース ウィンドウが表示されていない場合は、選択 * * * ビュー -&gt;その他の Windows-&gt;データ ソース**</span><span class="sxs-lookup"><span data-stu-id="b765e-229">Click **Finish.**
*If the Data Sources window is not showing up, select***View -&gt; Other Windows-&gt; Data Sources**</span></span>
-   <span data-ttu-id="b765e-230">キーを押して、データ ソース ウィンドウが自動しないように、ピン アイコンを非表示にします。</span><span class="sxs-lookup"><span data-stu-id="b765e-230">Press the pin icon, so the Data Sources window does not auto hide.</span></span> <span data-ttu-id="b765e-231">ウィンドウが表示された場合は、更新ボタンをクリックする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b765e-231">You may need to hit the refresh button if the window was already visible.</span></span>

    ![DataSource2](~/ef6/media/datasource2.png)

-   <span data-ttu-id="b765e-233">ソリューション エクスプ ローラーで、 **Form1.cs**メイン フォームをデザイナーで開くファイル。</span><span class="sxs-lookup"><span data-stu-id="b765e-233">In Solution Explorer, double-click the **Form1.cs** file to open the main form in designer.</span></span>
-   <span data-ttu-id="b765e-234">選択、**カテゴリ**データ ソースをフォームにドラッグします。</span><span class="sxs-lookup"><span data-stu-id="b765e-234">Select the **Category** data source and drag it on the form.</span></span> <span data-ttu-id="b765e-235">既定で新しい DataGridView (**categoryDataGridView**) ナビゲーション ツール バー コントロールは、デザイナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="b765e-235">By default, a new DataGridView (**categoryDataGridView**) and Navigation toolbar controls are added to the designer.</span></span> <span data-ttu-id="b765e-236">これらのコントロールは BindingSource にバインドされます (**categoryBindingSource**) とバインドのナビゲーター (**categoryBindingNavigator**) も作成されるコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="b765e-236">These controls are bound to the BindingSource (**categoryBindingSource**) and Binding Navigator (**categoryBindingNavigator**) components that are created as well.</span></span>
-   <span data-ttu-id="b765e-237">列の編集、 **categoryDataGridView**します。</span><span class="sxs-lookup"><span data-stu-id="b765e-237">Edit the columns on the **categoryDataGridView**.</span></span> <span data-ttu-id="b765e-238">設定する、 **CategoryId**列を読み取り専用にします。</span><span class="sxs-lookup"><span data-stu-id="b765e-238">We want to set the **CategoryId** column to read-only.</span></span> <span data-ttu-id="b765e-239">値、 **CategoryId**プロパティは、データを保存した後に、データベースによって生成されます。</span><span class="sxs-lookup"><span data-stu-id="b765e-239">The value for the **CategoryId** property is generated by the database after we save the data.</span></span>
    -   <span data-ttu-id="b765e-240">DataGridView コントロールを右クリックし、列の編集を選択してください.</span><span class="sxs-lookup"><span data-stu-id="b765e-240">Right-click the DataGridView control and select Edit Columns…</span></span>
    -   <span data-ttu-id="b765e-241">CategoryId 列を選択し、ReadOnly を True に設定します。</span><span class="sxs-lookup"><span data-stu-id="b765e-241">Select the CategoryId column and set ReadOnly to True</span></span>
    -   <span data-ttu-id="b765e-242">[Ok]</span><span class="sxs-lookup"><span data-stu-id="b765e-242">Press OK</span></span>
-   <span data-ttu-id="b765e-243">カテゴリのデータ ソースから製品を選択し、フォームにドラッグします。</span><span class="sxs-lookup"><span data-stu-id="b765e-243">Select Products from under the Category data source and drag it on the form.</span></span> <span data-ttu-id="b765e-244">ProductDataGridView と productBindingSource は、フォームに追加されます。</span><span class="sxs-lookup"><span data-stu-id="b765e-244">The productDataGridView and productBindingSource are added to the form.</span></span>
-   <span data-ttu-id="b765e-245">ProductDataGridView で列を編集します。</span><span class="sxs-lookup"><span data-stu-id="b765e-245">Edit the columns on the productDataGridView.</span></span> <span data-ttu-id="b765e-246">CategoryId およびカテゴリ列を非表示にして、読み取り専用に ProductId を設定します。</span><span class="sxs-lookup"><span data-stu-id="b765e-246">We want to hide the CategoryId and Category columns and set ProductId to read-only.</span></span> <span data-ttu-id="b765e-247">ProductId プロパティの値は、データを保存した後に、データベースによって生成されます。</span><span class="sxs-lookup"><span data-stu-id="b765e-247">The value for the ProductId property is generated by the database after we save the data.</span></span>
    -   <span data-ttu-id="b765e-248">DataGridView コントロールを右クリックして**列の編集.**.</span><span class="sxs-lookup"><span data-stu-id="b765e-248">Right-click the DataGridView control and select **Edit Columns...**.</span></span>
    -   <span data-ttu-id="b765e-249">選択、 **ProductId**およびセット列**ReadOnly**に**True**します。</span><span class="sxs-lookup"><span data-stu-id="b765e-249">Select the **ProductId** column and set **ReadOnly** to **True**.</span></span>
    -   <span data-ttu-id="b765e-250">選択、 **CategoryId**列とキーを押して、**削除**ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="b765e-250">Select the **CategoryId** column and press the **Remove** button.</span></span> <span data-ttu-id="b765e-251">これにより、**カテゴリ**列。</span><span class="sxs-lookup"><span data-stu-id="b765e-251">Do the same with the **Category** column.</span></span>
    -   <span data-ttu-id="b765e-252">**[OK]** を押します。</span><span class="sxs-lookup"><span data-stu-id="b765e-252">Press **OK**.</span></span>

    <span data-ttu-id="b765e-253">これまでに DataGridView コントロールに関連付けられている BindingSource コンポーネントをデザイナー。</span><span class="sxs-lookup"><span data-stu-id="b765e-253">So far, we associated our DataGridView controls with BindingSource components in the designer.</span></span> <span data-ttu-id="b765e-254">次のセクションのコードに追加のコードが categoryBindingSource.DataSource を現在 DbContext で追跡されるエンティティのコレクションに設定します。</span><span class="sxs-lookup"><span data-stu-id="b765e-254">In the next section we will add code to the code behind to set categoryBindingSource.DataSource to the collection of entities that are currently tracked by DbContext.</span></span> <span data-ttu-id="b765e-255">ときに製品 categoryBindingSource と productsBindingSource.DataMember プロパティに productsBindingSource.DataSource プロパティを設定した製品、WinForms、カテゴリの下にドラッグ アンド ドロップして、処理します。</span><span class="sxs-lookup"><span data-stu-id="b765e-255">When we dragged-and-dropped Products from under the Category, the WinForms took care of setting up the productsBindingSource.DataSource property to categoryBindingSource and productsBindingSource.DataMember property to Products.</span></span> <span data-ttu-id="b765e-256">このバインドでは、ため、productDataGridView で現在選択されているカテゴリに属する製品だけが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b765e-256">Because of this binding, only the products that belong to the currently selected Category will be displayed in the productDataGridView.</span></span>
-   <span data-ttu-id="b765e-257">有効にする、**保存**マウスの右ボタンをクリックして選択ナビゲーション ツールバーのボタン**有効**します。</span><span class="sxs-lookup"><span data-stu-id="b765e-257">Enable the **Save** button on the Navigation toolbar by clicking the right mouse button and selecting **Enabled**.</span></span>

    ![Form1 デザイナー](~/ef6/media/form1-designer.png)

-   <span data-ttu-id="b765e-259">保存のイベント ハンドラーを追加 ボタンをダブルクリックしてボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="b765e-259">Add the event handler for the save button by double-clicking on the button.</span></span> <span data-ttu-id="b765e-260">イベント ハンドラーに追加され、フォームの分離コードに移動されます。</span><span class="sxs-lookup"><span data-stu-id="b765e-260">This will add the event handler and bring you to the code behind for the form.</span></span> <span data-ttu-id="b765e-261">コード、 **categoryBindingNavigatorSaveItem\_クリックして**イベント ハンドラーは、次のセクションで追加されます。</span><span class="sxs-lookup"><span data-stu-id="b765e-261">The code for the **categoryBindingNavigatorSaveItem\_Click** event handler will be added in the next section.</span></span>

## <a name="add-the-code-that-handles-data-interaction"></a><span data-ttu-id="b765e-262">データの操作を処理するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="b765e-262">Add the Code that Handles Data Interaction</span></span>

<span data-ttu-id="b765e-263">これで、ProductContext を使用して、データ アクセスを実行するコード追加します。</span><span class="sxs-lookup"><span data-stu-id="b765e-263">We'll now add the code to use the ProductContext to perform data access.</span></span> <span data-ttu-id="b765e-264">次に示すように、メイン フォーム ウィンドウのコードを更新します。</span><span class="sxs-lookup"><span data-stu-id="b765e-264">Update the code for the main form window as shown below.</span></span>

<span data-ttu-id="b765e-265">コードでは、ProductContext の実行時間の長いインスタンスを宣言します。</span><span class="sxs-lookup"><span data-stu-id="b765e-265">The code declares a long-running instance of ProductContext.</span></span> <span data-ttu-id="b765e-266">ProductContext オブジェクトは、クエリやデータをデータベースに保存に使用されます。</span><span class="sxs-lookup"><span data-stu-id="b765e-266">The ProductContext object is used to query and save data to the database.</span></span> <span data-ttu-id="b765e-267">ProductContext インスタンスの Dispose() メソッドは、オーバーライドされた OnClosing メソッドから呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b765e-267">The Dispose() method on the ProductContext instance is then called from the overridden OnClosing method.</span></span> <span data-ttu-id="b765e-268">コード コメントでは、コードの動作について詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="b765e-268">The code comments provide details about what the code does.</span></span>

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

## <a name="test-the-windows-forms-application"></a><span data-ttu-id="b765e-269">Windows フォーム アプリケーションをテストします。</span><span class="sxs-lookup"><span data-stu-id="b765e-269">Test the Windows Forms Application</span></span>

-   <span data-ttu-id="b765e-270">コンパイルと実行して、アプリケーションが機能をテストできます。</span><span class="sxs-lookup"><span data-stu-id="b765e-270">Compile and run the application and you can test out the functionality.</span></span>

    ![Form1BeforeSave](~/ef6/media/form1beforesave.png)

-   <span data-ttu-id="b765e-272">保存した後にストア生成キーを画面に表示しています。</span><span class="sxs-lookup"><span data-stu-id="b765e-272">After saving the store generated keys are shown on the screen.</span></span>

    ![Form1AfterSave](~/ef6/media/form1aftersave.png)

-   <span data-ttu-id="b765e-274">Code First を使用した場合も表示されますが、 **WinFormswithEFSample.ProductContext**データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b765e-274">If you used Code First, then you will also see that a **WinFormswithEFSample.ProductContext** database is created for you.</span></span>

    ![ServerObjExplorer](~/ef6/media/serverobjexplorer.png)
