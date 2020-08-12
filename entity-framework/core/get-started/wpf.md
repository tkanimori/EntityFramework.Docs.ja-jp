---
title: WPF の使用を開始する - EF Core
author: jeremylikness
ms.author: jeliknes
ms.date: 07/24/2020
uid: core/get-started/wpf
ms.openlocfilehash: 958fc579a72a9bf3c97c5551d55777df6c32293f
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535591"
---
# <a name="getting-started-with-wpf"></a><span data-ttu-id="59cf7-102">WPF の概要</span><span class="sxs-lookup"><span data-stu-id="59cf7-102">Getting Started with WPF</span></span>

<span data-ttu-id="59cf7-103">このステップ バイ ステップ チュートリアルでは、"メイン - 詳細" フォームで POCO 型を WPF コントロールにバインドする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-103">This step-by-step walkthrough shows how to bind POCO types to WPF controls in a "main-detail" form.</span></span> <span data-ttu-id="59cf7-104">アプリケーションでは、Entity Framework API を使用して、データベースのデータをオブジェクトに設定し、変更を追跡し、データベースにデータを保持します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-104">The application uses the Entity Framework APIs to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="59cf7-105">このモデルでは、一対多のリレーションシップに関係する 2 つの型が定義されています: **Category** (プリンシパル\\メイン) と **Product** (依存\\詳細)。</span><span class="sxs-lookup"><span data-stu-id="59cf7-105">The model defines two types that participate in one-to-many relationship: **Category** (principal\\main) and **Product** (dependent\\detail).</span></span> <span data-ttu-id="59cf7-106">WPF データ バインディング フレームワークを使用すると、関連するオブジェクト間のナビゲーションが可能になります。マスター ビューで行を選択すると、対応する子データで詳細ビューが更新されます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-106">The WPF data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="59cf7-107">このチュートリアルのスクリーンショットとコード リストは、Visual Studio 2019 16.6.5 から取得されています。</span><span class="sxs-lookup"><span data-stu-id="59cf7-107">The screen shots and code listings in this walkthrough are taken from Visual Studio 2019 16.6.5.</span></span>

> [!TIP]
> <span data-ttu-id="59cf7-108">この記事の[サンプルは GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF) で確認できます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-108">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF).</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="59cf7-109">前提条件</span><span class="sxs-lookup"><span data-stu-id="59cf7-109">Pre-Requisites</span></span>

* <span data-ttu-id="59cf7-110">このチュートリアルを完了するには、Visual Studio 2019 16.3 以降がインストールされ、 **.NET デスクトップ ワークロード**が選択されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="59cf7-110">You need to have Visual Studio 2019 16.3 or later installed with the **.NET desktop workload** selected to complete this walkthrough.</span></span>
    
    <span data-ttu-id="59cf7-111">Visual Studio の最新バージョンのインストールの詳細については、「[Visual Studio のインストール](/visualstudio/install/install-visual-studio)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="59cf7-111">For more information about installing the latest version of Visual Studio, see [Install Visual Studio](/visualstudio/install/install-visual-studio).</span></span>

## <a name="create-the-application"></a><span data-ttu-id="59cf7-112">アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="59cf7-112">Create the Application</span></span>

1. <span data-ttu-id="59cf7-113">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="59cf7-113">Open Visual Studio</span></span>
2. <span data-ttu-id="59cf7-114">スタート ウィンドウで、**[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-114">On the start window, choose **Create new project**.</span></span>
3. <span data-ttu-id="59cf7-115">"WPF" を検索し、 **[WPF アプリ (.NET Core)]** を選択して、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-115">Search for "WPF," choose **WPF App (.NET Core)** and then choose **Next**.</span></span>
4. <span data-ttu-id="59cf7-116">次の画面で、プロジェクトの名前を指定し (例: **GetStartedWPF**)、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-116">At the next screen, give the project a name, for example, **GetStartedWPF**, and choose **Create.**</span></span>

## <a name="install-the-entity-framework-nuget-packages"></a><span data-ttu-id="59cf7-117">Entity Framework NuGet パッケージをインストールする</span><span class="sxs-lookup"><span data-stu-id="59cf7-117">Install the Entity Framework NuGet packages</span></span>

1. <span data-ttu-id="59cf7-118">ソリューションを右クリックして、 **[ソリューションの NuGet パッケージの管理...]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-118">Right-click on the solution and choose **Manage NuGet Packages for Solution...**</span></span>

    ![NuGet パッケージの管理](_static/wpf-tutorial-nuget.jpg)

1. <span data-ttu-id="59cf7-120">検索ボックスに、「`entityframeworkcore.sqlite`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-120">Type `entityframeworkcore.sqlite` in the search box.</span></span>
1. <span data-ttu-id="59cf7-121">**Microsoft.EntityFrameworkCore.Sqlite** パッケージを選択します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-121">Select the **Microsoft.EntityFrameworkCore.Sqlite** package.</span></span>
1. <span data-ttu-id="59cf7-122">右側のペインでプロジェクトを確認し、 **[インストール]** をクリックします</span><span class="sxs-lookup"><span data-stu-id="59cf7-122">Check the project in the right pane and click **Install**</span></span>

    ![Sqlite パッケージ](_static/wpf-tutorial-sqlite.jpg)

1. <span data-ttu-id="59cf7-124">同じようにして、`entityframeworkcore.proxies` を検索し、**Microsoft.EntityFrameworkCore.Proxies** をインストールします。</span><span class="sxs-lookup"><span data-stu-id="59cf7-124">Repeat the steps to search for `entityframeworkcore.proxies` and install **Microsoft.EntityFrameworkCore.Proxies**.</span></span>

> [!NOTE]
> <span data-ttu-id="59cf7-125">Sqlite パッケージをインストールすると、関連する **Microsoft.EntityFrameworkCore** 基本パッケージが自動的にプルされます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-125">When you installed the Sqlite package, it automatically pulled down the related **Microsoft.EntityFrameworkCore** base package.</span></span> <span data-ttu-id="59cf7-126">**Microsoft.EntityFrameworkCore.Proxies** パッケージでは、データの "遅延読み込み" がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="59cf7-126">The **Microsoft.EntityFrameworkCore.Proxies** package provides support for "lazy-loading" data.</span></span> <span data-ttu-id="59cf7-127">これは、子エンティティを持つエンティティがある場合、最初の読み込みでは親のみがフェッチされることを意味します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-127">This means when you have entities with child entities, only the parents are fetched on the initial load.</span></span> <span data-ttu-id="59cf7-128">子エンティティにアクセスしようとすると、プロキシによって検出され、必要に応じて自動的に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-128">The proxies detect when an attempt to access the child entities is made and automatically loads them on demand.</span></span> 

## <a name="define-a-model"></a><span data-ttu-id="59cf7-129">モデルを定義する</span><span class="sxs-lookup"><span data-stu-id="59cf7-129">Define a Model</span></span>

<span data-ttu-id="59cf7-130">このチュートリアルでは、"Code First" を使用してモデルを実装します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-130">In this walkthrough you will implement a model using "code first."</span></span> <span data-ttu-id="59cf7-131">つまり、定義した C# クラスに基づいて、EF Core によりデータベース テーブルとスキーマが作成されます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-131">This means that EF Core will create the database tables and schema based on the C# classes you define.</span></span>

<span data-ttu-id="59cf7-132">新しいクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-132">Add a new class.</span></span> <span data-ttu-id="59cf7-133">`Product.cs` という名前を指定し、次のように設定します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-133">Give it the name: `Product.cs` and populate it like this:</span></span>

**`Product.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Product.cs)]

<span data-ttu-id="59cf7-134">次に、`Category.cs` という名前のクラスを追加し、次のコードを入力します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-134">Next, add a class named `Category.cs` and populate it with the following code:</span></span>

**`Category.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Category.cs)]

<span data-ttu-id="59cf7-135">**Category** クラスの **Products** プロパティと、**Product** クラスの **Category** プロパティは、ナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="59cf7-135">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="59cf7-136">Entity Framework では、ナビゲーション プロパティによって、2 つのエンティティ型間のリレーションシップをナビゲートする手段が提供されます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-136">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="59cf7-137">エンティティを定義するだけでなく、DbContext から派生して DbSet&lt;TEntity&gt; プロパティを公開するクラスを定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59cf7-137">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="59cf7-138">DbSet&lt;TEntity&gt; プロパティにより、モデルに含める型をコンテキストに認識させることができます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-138">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="59cf7-139">DbContext の派生型のインスタンスによって、実行時にエンティティ オブジェクトが管理されます。これには、オブジェクトへのデータベースのデータの設定、変更の追跡、データベースへのデータの保持が含まれます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-139">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="59cf7-140">次のような定義の新しい `ProductContext.cs` クラスを、プロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-140">Add a new `ProductContext.cs` class to the project with the following definition:</span></span>

**`ProductContext.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/ProductContext.cs)]

* <span data-ttu-id="59cf7-141">`DbSet` により、データベースにマップする必要のある C# エンティティが EF Core に示されます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-141">The `DbSet` informs EF Core what C# entities should be mapped to the database.</span></span>
* <span data-ttu-id="59cf7-142">EF Core の `DbContext` を構成するには、さまざまな方法があります。</span><span class="sxs-lookup"><span data-stu-id="59cf7-142">There are a variety of ways to configure the EF Core `DbContext`.</span></span> <span data-ttu-id="59cf7-143">詳しくは次を参照してください: 「[DbContext の構成](/ef/core/miscellaneous/configuring-dbcontext)」。</span><span class="sxs-lookup"><span data-stu-id="59cf7-143">You can read about them in: [Configuring a DbContext](/ef/core/miscellaneous/configuring-dbcontext).</span></span>
* <span data-ttu-id="59cf7-144">この例では、`OnConfiguring` のオーバーライドを使用して、Sqlite データ ファイルを指定します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-144">This example uses the `OnConfiguring` override to specify a Sqlite data file.</span></span>
* <span data-ttu-id="59cf7-145">`UseLazyLoadingProxies` の呼び出しにより、遅延読み込みを実装するよう EF Core に指示されるため、子エンティティは親からアクセスされた時点で自動的に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-145">The `UseLazyLoadingProxies` call tells EF Core to implement lazy-loading, so child entities are automatically loaded when accessed from the parent.</span></span>

<span data-ttu-id="59cf7-146">**Ctrl + Shift + B** キーを押すか、 **[ビルド] &gt; [ソリューションのビルド]** に移動して、プロジェクトをコンパイルします。</span><span class="sxs-lookup"><span data-stu-id="59cf7-146">Press **CTRL+SHIFT+B** or navigate to **Build &gt; Build Solution** to compile the project.</span></span>

> [!TIP]
> <span data-ttu-id="59cf7-147">データベースと EF Core モデルの同期を維持するためのさまざまな方法について確認します: 「[データベース スキーマを管理する](/ef/core/managing-schemas)」。</span><span class="sxs-lookup"><span data-stu-id="59cf7-147">Learn about the different was to keep your database and EF Core models in sync: [Managing Database Schemas](/ef/core/managing-schemas).</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="59cf7-148">遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="59cf7-148">Lazy Loading</span></span>

<span data-ttu-id="59cf7-149">**Category** クラスの **Products** プロパティと、**Product** クラスの **Category** プロパティは、ナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="59cf7-149">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="59cf7-150">Entity Framework Core では、ナビゲーション プロパティによって、2 つのエンティティ型間のリレーションシップをナビゲートする手段が提供されます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-150">In Entity Framework Core, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="59cf7-151">EF Core を使用すると、ナビゲーション プロパティに初めてアクセスしたときに、関連エンティティをデータベースから自動的に読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-151">EF Core gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="59cf7-152">この種類の読み込み (遅延読み込みと呼ばれます) では、各ナビゲーション プロパティに初めてアクセスしたときに、コンテンツがコンテキスト内に存在しない場合、データベースに対して別のクエリが実行されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="59cf7-152">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="59cf7-153">"単純な従来の C# オブジェクト" (POCO) エンティティ型を使用すると、EF Core では、実行中に派生プロキシ型のインスタンスを作成してから、クラス内の仮想プロパティをオーバーライドして読み込みフックを追加することにより、遅延読み込みが実現されます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-153">When using "Plain Old C# Object" (POCO) entity types, EF Core achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="59cf7-154">関連オブジェクトの遅延読み込みを行うには、ナビゲーション プロパティ ゲッターを **public** および **virtual** (Visual Basic では **Overridable**) として宣言する必要があり、クラスを **sealed** (Visual Basic では **NotOverridable**) にしないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="59cf7-154">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and your class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="59cf7-155">Database First を使用すると、遅延読み込みを有効にするために、ナビゲーション プロパティは自動的に virtual にされます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-155">When using Database First, navigation properties are automatically made virtual to enable lazy loading.</span></span> 

## <a name="bind-object-to-controls"></a><span data-ttu-id="59cf7-156">オブジェクトをコントロールにバインドする</span><span class="sxs-lookup"><span data-stu-id="59cf7-156">Bind Object to Controls</span></span>

<span data-ttu-id="59cf7-157">モデルで定義されているクラスを、この WPF アプリケーションのデータ ソースとして追加します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-157">Add the classes that are defined in the model as data sources for this WPF application.</span></span>

1. <span data-ttu-id="59cf7-158">ソリューション エクスプローラーで **MainWindow.xaml** をダブルクリックして、メイン フォームを開きます</span><span class="sxs-lookup"><span data-stu-id="59cf7-158">Double-click **MainWindow.xaml** in Solution Explorer to open the main form</span></span>
1. <span data-ttu-id="59cf7-159">**[XAML]** タブを選択して、XAML を編集します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-159">Choose the **XAML** tab to edit the XAML.</span></span>
1. <span data-ttu-id="59cf7-160">開始 `Window` タグの直後に次のソースを追加して、EF Core エンティティに接続します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-160">Immediately after the opening `Window` tag, add the following sources to connect to the EF Core entities.</span></span>

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=1-13&highlight=9-13)]

1. <span data-ttu-id="59cf7-161">これにより、"親" カテゴリのソースと、"詳細" 製品の 2 番目のソースが設定されます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-161">This sets up source for the "parent" categories, and second source for the "detail" products.</span></span>
1. <span data-ttu-id="59cf7-162">次に、XAML の終了 `Window.Resources` タグの後に、次のマークアップを追加します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-162">Next, add the following markup to your XAML after the closing `Window.Resources` tag.</span></span>

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=15-26)]

1. <span data-ttu-id="59cf7-163">`CategoryId` はデータベースによって割り当てられ、変更できないので、`ReadOnly` に設定されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="59cf7-163">Note that the `CategoryId` is set to `ReadOnly` because it is assigned by the database and cannot be changed.</span></span>

## <a name="adding-a-details-grid"></a><span data-ttu-id="59cf7-164">詳細グリッドの追加</span><span class="sxs-lookup"><span data-stu-id="59cf7-164">Adding a Details Grid</span></span>

<span data-ttu-id="59cf7-165">カテゴリを表示するグリッドができたので、製品を表示するための詳細グリッドを追加できます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-165">Now that the grid exists to display categories, the details grid can be added to show products.</span></span>

**`MainWindow.xaml`**

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=27-40)]

<span data-ttu-id="59cf7-166">最後に、`Save` ボタンを追加して、`Button_Click` にクリック イベントを結び付けます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-166">Finally, add a `Save` button and wire in the click event to `Button_Click`.</span></span>

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=41-42)]

<span data-ttu-id="59cf7-167">デザイン ビューは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="59cf7-167">Your design view should look like this:</span></span>

![WPF デザイナーのスクリーンショット](_static/wpf-tutorial-designer.jpg)

## <a name="add-code-that-handles-data-interaction"></a><span data-ttu-id="59cf7-169">データのやり取りを処理するコードを追加する</span><span class="sxs-lookup"><span data-stu-id="59cf7-169">Add Code that Handles Data Interaction</span></span>

<span data-ttu-id="59cf7-170">次に、いくつかのイベント ハンドラーをメイン ウィンドウに追加します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-170">It's time to add some event handlers to the main window.</span></span>

1. <span data-ttu-id="59cf7-171">XAML ウィンドウで、 **&lt;Window&gt;** 要素をクリックして、メイン ウィンドウを選択します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-171">In the XAML window, click on the **&lt;Window&gt;** element, to select the main window.</span></span>
1. <span data-ttu-id="59cf7-172">**[プロパティ]** ウィンドウで右上の **[イベント]** をクリックし、 **[Loaded]** ラベルの右にあるテキスト ボックスをダブルクリックします。</span><span class="sxs-lookup"><span data-stu-id="59cf7-172">In the **Properties** window choose **Events** at the top right, then double-click the text box to right of the **Loaded** label.</span></span>

    ![メイン ウィンドウのプロパティ](_static/wpf-tutorial-loaded.jpg)

<span data-ttu-id="59cf7-174">これにより、フォームのコード ビハインドが表示されます。`ProductContext` を使用してデータ アクセスを実行するように、コードを編集します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-174">This brings you to the code behind for the form, we'll now edit the code to use the `ProductContext` to perform data access.</span></span> <span data-ttu-id="59cf7-175">次に示すようにコードを更新します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-175">Update the code as shown below.</span></span>

<span data-ttu-id="59cf7-176">このコードでは、`ProductContext` の実行時間の長いインスタンスが宣言されています。</span><span class="sxs-lookup"><span data-stu-id="59cf7-176">The code declares a long-running instance of `ProductContext`.</span></span> <span data-ttu-id="59cf7-177">データをクエリしてデータベースに保存するには、`ProductContext` オブジェクトを使用します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-177">The `ProductContext` object is used to query and save data to the database.</span></span> <span data-ttu-id="59cf7-178">その後、`ProductContext` インスタンスの `Dispose()` メソッドを、オーバーライドされた `OnClosing` メソッドから呼び出します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-178">The `Dispose()` method on the `ProductContext` instance is then called from the overridden `OnClosing` method.</span></span><span data-ttu-id="59cf7-179">コードのコメントで各ステップの動作内容が説明されています。</span><span class="sxs-lookup"><span data-stu-id="59cf7-179"> The code comments explain what each step does.</span></span>

**`MainWindow.xaml.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml.cs)]

> [!NOTE]
> <span data-ttu-id="59cf7-180">このコードでは、`EnsureCreated()` の呼び出しを使用して、最初の実行時にデータベースを構築します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-180">The code uses a call to `EnsureCreated()` to build the database on the first run.</span></span> <span data-ttu-id="59cf7-181">これはデモでは許されますが、運用アプリでは、[移行](/ef/core/managing-schemas/migrations/)を調べてスキーマを管理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59cf7-181">This is acceptable for demos, but in production apps you should look at [migrations](/ef/core/managing-schemas/migrations/) to manage your schema.</span></span> <span data-ttu-id="59cf7-182">また、このコードは、ローカルの SQLite データベースを使用しているため、同期的に実行されます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-182">The code also executes synchronously because it uses a local SQLite database.</span></span> <span data-ttu-id="59cf7-183">リモート サーバーが含まれるのが一般的である運用シナリオでは、`Load` および `SaveChanges` メソッドの非同期バージョンを使用することを検討します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-183">For production scenarios that typically involve a remote server, consider using the asynchronous versions of the `Load` and `SaveChanges` methods.</span></span>

## <a name="test-the-wpf-application"></a><span data-ttu-id="59cf7-184">WPF アプリケーションをテストする</span><span class="sxs-lookup"><span data-stu-id="59cf7-184">Test the WPF Application</span></span>

<span data-ttu-id="59cf7-185">**F5** キーを押すか、 **[デバッグ] &gt; [デバッグの開始]** を選択し、アプリケーションをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-185">Compile and run the application by pressing **F5** or choosing **Debug &gt; Start Debugging**.</span></span> <span data-ttu-id="59cf7-186">`products.db` という名前のファイルでデータベースが自動的に作成されるはずです。</span><span class="sxs-lookup"><span data-stu-id="59cf7-186">The database should be automatically created with a file named `products.db`.</span></span> <span data-ttu-id="59cf7-187">カテゴリ名を入力して Enter キーを押し、下のグリッドに製品を追加します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-187">Enter a category name and hit enter, then add products to the lower grid.</span></span> <span data-ttu-id="59cf7-188">[保存] をクリックし、データベースで指定された ID でグリッドが更新されるのを確認します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-188">Click save and watch the grid refresh with the database provided ids.</span></span> <span data-ttu-id="59cf7-189">行を強調表示にして **Delete** キーを押し、行を削除します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-189">Highlight a row and hit **Delete** to remove the row.</span></span> <span data-ttu-id="59cf7-190">**[保存]** をクリックすると、エンティティが削除されます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-190">The entity will be deleted when you click **Save**.</span></span>

![実行中のアプリケーション](_static/wpf-tutorial-app.jpg)

## <a name="property-change-notification"></a><span data-ttu-id="59cf7-192">プロパティの変更通知</span><span class="sxs-lookup"><span data-stu-id="59cf7-192">Property Change Notification</span></span>

<span data-ttu-id="59cf7-193">この例では、4 つのステップでエンティティと UI を同期させています。</span><span class="sxs-lookup"><span data-stu-id="59cf7-193">This example relies on four steps to synchronize the entities with the UI.</span></span> 

1. <span data-ttu-id="59cf7-194">最初の `_context.Categories.Load()` の呼び出しで、カテゴリ データが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-194">The initial call `_context.Categories.Load()` loads the categories data.</span></span>
1. <span data-ttu-id="59cf7-195">遅延読み込みプロキシにより、依存製品データが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-195">The lazy-loading proxies load the dependent products data.</span></span>
1. <span data-ttu-id="59cf7-196">`_context.SaveChanges()` を呼び出すと、EF Core に組み込まれている変更追跡により、挿入や削除など、エンティティに対して必要な変更が行われます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-196">EF Core's built-in change tracking makes the necessary modifications to entities, including insertions and deletions, when `_context.SaveChanges()` is called.</span></span>
1. <span data-ttu-id="59cf7-197">`DataGridView.Items.Refresh()` を呼び出すと、新しく生成された ID で強制的に再読み込みが行われます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-197">The calls to `DataGridView.Items.Refresh()` force a reload with the newly generated ids.</span></span>

<span data-ttu-id="59cf7-198">これは、この作業開始サンプルには使用できますが、他のシナリオでは追加のコードが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="59cf7-198">This works for our getting started sample, but you may require additional code for other scenarios.</span></span> <span data-ttu-id="59cf7-199">WPF コントロールにより、エンティティのフィールドとプロパティが読み取られて、UI がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-199">WPF controls render the UI by reading the fields and properties on your entities.</span></span> <span data-ttu-id="59cf7-200">ユーザー インターフェイス (UI) で値を編集すると、その値がエンティティに渡されます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-200">When you edit a value in the user interface (UI), that value is passed to your entity.</span></span> <span data-ttu-id="59cf7-201">データベースから読み込むなどして、エンティティのプロパティの値を直接変更すると、WPF によって変更が UI にすぐには反映されません。</span><span class="sxs-lookup"><span data-stu-id="59cf7-201">When you change the value of a property directly on your entity, such as loading it from the database, WPF will not immediately reflect the changes in the UI.</span></span> <span data-ttu-id="59cf7-202">レンダリング エンジンに変更が通知される必要があります。</span><span class="sxs-lookup"><span data-stu-id="59cf7-202">The rendering engine must be notified of the changes.</span></span> <span data-ttu-id="59cf7-203">このプロジェクトでは、手動で `Refresh()` を呼び出すことによってこれを行いました。</span><span class="sxs-lookup"><span data-stu-id="59cf7-203">The project did this by manually calling `Refresh()`.</span></span> <span data-ttu-id="59cf7-204">この通知を自動化する簡単な方法は、[INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) インターフェイスを実装することです。</span><span class="sxs-lookup"><span data-stu-id="59cf7-204">An easy way to automate this notification is by implementing the [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) interface.</span></span> <span data-ttu-id="59cf7-205">WPF のコンポーネントにより、自動的にインターフェイスが検出されて、変更イベントに登録されます。</span><span class="sxs-lookup"><span data-stu-id="59cf7-205">WPF components will automatically detect the interface and register for change events.</span></span> <span data-ttu-id="59cf7-206">エンティティでは、これらのイベントを発生させる必要があります。</span><span class="sxs-lookup"><span data-stu-id="59cf7-206">The entity is responsible for raising these events.</span></span> 

> [!TIP]
> <span data-ttu-id="59cf7-207">変更の処理方法の詳細については、以下を参照してください: [プロパティ変更通知の実装方法](/dotnet/framework/wpf/data/how-to-implement-property-change-notification)に関するページ。</span><span class="sxs-lookup"><span data-stu-id="59cf7-207">To learn more about how to handle changes, read: [How to implement property change notification](/dotnet/framework/wpf/data/how-to-implement-property-change-notification).</span></span>

## <a name="next-steps"></a><span data-ttu-id="59cf7-208">次の手順</span><span class="sxs-lookup"><span data-stu-id="59cf7-208">Next Steps</span></span>

<span data-ttu-id="59cf7-209">[DbContext の構成](/ef/core/miscellaneous/configuring-dbcontext)の詳細について確認します。</span><span class="sxs-lookup"><span data-stu-id="59cf7-209">Learn more about [Configuring a DbContext](/ef/core/miscellaneous/configuring-dbcontext).</span></span>