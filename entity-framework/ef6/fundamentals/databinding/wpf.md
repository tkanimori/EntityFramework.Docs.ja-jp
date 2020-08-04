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
# <a name="databinding-with-wpf"></a>WPF とのデータバインド

> [!IMPORTANT]
> **このドキュメントは、.NET Framework の WPF でのみ有効です**
>
> このドキュメントでは、.NET Framework での WPF のデータバインドについて説明します。 新しい .NET Core プロジェクトの場合は、Entity Framework 6 ではなく[EF Core](/ef/core)を使用することをお勧めします。 EF Core でのデータバインドのドキュメントは、「 [WPF でのはじめに](/ef/core/get-started/wpf)」を参照してください。

このステップバイステップチュートリアルでは、"マスター/詳細" フォームで POCO 型を WPF コントロールにバインドする方法について説明します。 アプリケーションは、Entity Framework Api を使用して、データベースのデータをオブジェクトに読み込み、変更を追跡し、データベースにデータを保持します。

このモデルでは、1対多のリレーションシップに参加する2つの型が定義されています。**カテゴリ**(プリンシパル \\ マスター) と**製品**(依存関係の \\ 詳細) です。 次に、Visual Studio ツールを使用して、モデルで定義されている型を WPF コントロールにバインドします。 WPF データバインディングフレームワークを使用すると、関連するオブジェクト間のナビゲーションが可能になります。マスタービューで行を選択すると、対応する子データで詳細ビューが更新されます。

このチュートリアルのスクリーンショットとコードリストは Visual Studio 2013 から取得されていますが、Visual Studio 2012 または Visual Studio 2010 を使用してこのチュートリアルを完了することができます。

## <a name="use-the-object-option-for-creating-wpf-data-sources"></a>WPF データソースを作成するには、' Object ' オプションを使用します

以前のバージョンの Entity Framework では、EF デザイナーで作成されたモデルに基づいて新しいデータソースを作成するときに、**データベース**オプションを使用することをお勧めしました。 これは、デザイナーが ObjectContext および EntityObject から派生したエンティティクラスから派生したコンテキストを生成するためです。 データベースオプションを使用すると、この API サーフェイスと対話するための最適なコードを記述するのに役立ちます。

Visual Studio 2012 および Visual Studio 2013 用の EF デザイナーは、単純な POCO エンティティクラスと共に DbContext から派生するコンテキストを生成します。 Visual Studio 2010 では、このチュートリアルの後半で説明するように、DbContext を使用するコード生成テンプレートにスワップすることをお勧めします。

DbContext API サーフェイスを使用する場合は、このチュートリアルで示すように、新しいデータソースを作成するときに**オブジェクト**オプションを使用する必要があります。

必要に応じて、EF デザイナーで作成されたモデルの[ObjectContext ベースのコード生成に戻す](~/ef6/modeling/designer/codegen/legacy-objectcontext.md)ことができます。

## <a name="pre-requisites"></a>前提条件

このチュートリアルを完了するには、Visual Studio 2013、Visual Studio 2012、または Visual Studio 2010 がインストールされている必要があります。

Visual Studio 2010 を使用している場合は、NuGet もインストールする必要があります。 詳細については、「 [NuGet のインストール](https://docs.microsoft.com/nuget/install-nuget-client-tools)」を参照してください。  

## <a name="create-the-application"></a>アプリケーションを作成する

-   Visual Studio を開く
-   **ファイル- &gt; 新規 &gt; プロジェクト....**
-   左側のウィンドウで [ **Windows**] を選択し、   右側のウィンドウで **WPFApplication**を選択します。
-   名前として「 **WPFwithEFSample**」と入力し   ます。
-   [ **OK]** を選択

## <a name="install-the-entity-framework-nuget-package"></a>Entity Framework NuGet パッケージをインストールする

-   ソリューションエクスプローラーで、 **Winフォーム Withefsample**プロジェクトを右クリックします。
-   [ **NuGet パッケージの管理...** ] を選択します。
-   [NuGet パッケージの管理] ダイアログで、[**オンライン**] タブを選択し、 **entityframework**パッケージを選択します。
-   **[Install]** (インストール) をクリックします。  
    >[!NOTE]
    > EntityFramework アセンブリに加えて、System.componentmodel への参照も追加されます。 プロジェクトに system.object への参照が含まれている場合は、EntityFramework パッケージのインストール時に削除されます。 Entity Framework 6 アプリケーションでは、system.object アセンブリは使用されなくなりました。

## <a name="define-a-model"></a>モデルの定義

このチュートリアルでは、Code First または EF デザイナーを使用してモデルを実装することを選択できます。 次の2つのセクションのいずれかを実行します。

### <a name="option-1-define-a-model-using-code-first"></a>オプション 1: Code First を使用してモデルを定義する

このセクションでは、Code First を使用してモデルとそれに関連付けられたデータベースを作成する方法について説明します。 EF デザイナーを使用してデータベースからモデルをリバースエンジニアリングする場合に Database First を使用する場合は、次のセクション (「**オプション 2: Database First を使用してモデルを定義**する」) に進んでください。

Code First 開発を使用する場合は、通常、概念 (ドメイン) モデルを定義する .NET Framework クラスを記述することから始めます。

-   WPFwithEFSample に新しいクラスを追加し **ます。**
    -   プロジェクト名を右クリックします。
    -   [**追加**]、[**新しい項目**] の順に選択します。
    -   [**クラス**] を選択し、[クラス名] に「 **Product**」と入力し   ます。
-    **Product**   クラスの定義を次のコードに置き換えます。

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

**Product**クラスの**Category**クラスおよび**category**プロパティの**Products**プロパティは、ナビゲーションプロパティです。 Entity Framework では、ナビゲーションプロパティを使用して、2つのエンティティ型間のリレーションシップをナビゲートすることができます。

エンティティを定義するだけでなく、DbContext から派生し、Dbcontext TEntity プロパティを公開するクラスを定義する必要があり &lt; &gt; ます。 DbSet TEntity プロパティを使用すると、 &lt; &gt; モデルに含める型をコンテキストに認識させることができます。

DbContext の派生型のインスタンスは、実行時にエンティティオブジェクトを管理します。これには、データベースからのデータを使用したオブジェクトの読み込み、変更の追跡、およびデータベースへのデータの永続化が含まれます。

-   次の定義を使用して、プロジェクトに新しい**Productcontext**クラスを追加します。

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

プロジェクトをコンパイルします。

### <a name="option-2-define-a-model-using-database-first"></a>オプション 2: Database First を使用してモデルを定義する

このセクションでは、Database First を使用して、EF デザイナーを使用してデータベースからモデルをリバースエンジニアリングする方法について説明します。 前のセクション (**オプション 1: Code First を使用してモデルを定義する)** を完了している場合は、このセクションをスキップし、「**遅延読み込み**」セクションに直接移動します。

#### <a name="create-an-existing-database"></a>既存のデータベースを作成する

通常、既存のデータベースを対象とする場合は、既に作成されますが、このチュートリアルでは、にアクセスするデータベースを作成する必要があります。

Visual Studio と共にインストールされるデータベースサーバーは、インストールされている Visual Studio のバージョンによって異なります。

-   Visual Studio 2010 を使用している場合は、SQL Express データベースを作成します。
-   Visual Studio 2012 を使用している場合は、 [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx)データベースを作成します。

では、データベースを生成してみましょう。

-   **ビュー- &gt; サーバーエクスプローラー**
-   [**データ接続-接続の &gt; 追加**] を右クリックします。
-   サーバーエクスプローラーからデータベースに接続していない場合は、データソースとして Microsoft SQL Server を選択する必要があります。

    ![データ ソースの変更](~/ef6/media/changedatasource.png)

-   インストールされているものに応じて LocalDB または SQL Express に接続し、データベース名として「 **Products** 」と入力します。

    ![接続 LocalDB の追加](~/ef6/media/addconnectionlocaldb.png)

    ![接続 Express の追加](~/ef6/media/addconnectionexpress.png)

-   [ **OK]** を選択すると、新しいデータベースを作成するかどうかを確認するメッセージが表示されます。 [**はい**] を選択します。

    ![データベースの作成](~/ef6/media/createdatabase.png)

-   新しいデータベースがサーバーエクスプローラーに表示され、右クリックして [**新しいクエリ**] を選択します。
-   次の SQL を新しいクエリにコピーし、クエリを右クリックして、[**実行**] を選択します。

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

#### <a name="reverse-engineer-model"></a>リバースエンジニアリングモデル

ここでは、Visual Studio の一部として含まれている Entity Framework Designer を使用して、モデルを作成します。

-   **プロジェクト- &gt; 新しい項目の追加...**
-   左側のメニューから [**データ**] を選択し、[ADO.NET] をクリックし**Entity Data Model**
-   名前として「 **Productmodel** 」と入力し、[ **OK]** をクリックします。
-   **Entity Data Model ウィザード**が起動します。
-   [**データベースから生成**] を選択し、[**次へ**] をクリックします。

    ![モデル コンテンツの選択](~/ef6/media/choosemodelcontents.png)

-   最初のセクションで作成したデータベースへの接続を選択し、接続文字列の名前として「 **Productcontext** 」と入力して、[**次へ**] をクリックします。

    ![接続の選択](~/ef6/media/chooseyourconnection.png)

-   [テーブル] の横にあるチェックボックスをオンにしてすべてのテーブルをインポートし、[完了] をクリックします。

    ![オブジェクトの選択](~/ef6/media/chooseyourobjects.png)

リバースエンジニアリングプロセスが完了すると、新しいモデルがプロジェクトに追加され、Entity Framework Designer で表示できるように開かれます。 データベースの接続の詳細を含む App.config ファイルもプロジェクトに追加されています。

#### <a name="additional-steps-in-visual-studio-2010"></a>Visual Studio 2010 の追加手順

Visual Studio 2010 で作業している場合は、EF6 コード生成を使用するように EF デザイナーを更新する必要があります。

-   EF デザイナーでモデルの空の場所を右クリックし、[**コード生成項目の追加...** ] を選択します。
-   左側のメニューから [**オンラインテンプレート**] を選択し、 **dbcontext**を検索します。
-   **C の EF 6.X DbContext \# ジェネレーター**を選択し、名前として「製品**モデル**」と入力して、[追加] をクリックします。

#### <a name="updating-code-generation-for-data-binding"></a>データバインディングのコード生成の更新

EF は T4 テンプレートを使用して、モデルからコードを生成します。 Visual Studio に付属しているテンプレート、または Visual Studio ギャラリーからダウンロードしたテンプレートは、汎用的な使用を目的としています。 これは、これらのテンプレートから生成されたエンティティに単純な ICollection T プロパティがあることを意味 &lt; &gt; します。 ただし、WPF を使用してデータバインディングを実行する場合は、コレクションのプロパティに**system.collections.objectmodel.observablecollection**を使用することをお勧めします。これにより、wpf がコレクションに加えられた変更を追跡できるようになります。 この最終的には、System.collections.objectmodel.observablecollection を使用するようにテンプレートを変更します。

-   **ソリューションエクスプローラー**を開き、 **productmodel. .edmx**ファイルを検索します。
-   ProductModel. .edmx ファイルの下に入れ子になっている**ProductModel.tt**ファイルを見つけます。

    ![WPF 製品モデルテンプレート](~/ef6/media/wpfproductmodeltemplate.png)

-   ProductModel.tt ファイルをダブルクリックして、Visual Studio エディターで開きます。
-   "**ICollection**" の2つの出現箇所を検索し、"**system.collections.objectmodel.observablecollection**" に置き換えます。 これらは、約296行と484行にあります。
-   最初に出現する "**HashSet**" を検索し、"**system.collections.objectmodel.observablecollection**" に置き換えます。 この現象は約50行目にあります。 コードの後半で見つかった HashSet の2回目の出現を置き換え**ないでください**。
-   "System.string" だけを検索し、**"system.string****. objectmodel**" に置換します。 これは約424行目にあります。
-   ProductModel.tt ファイルを保存します。 これにより、エンティティのコードが再生成されます。 コードが自動的に再生成されない場合は、ProductModel.tt を右クリックし、[カスタムツールの実行] を選択します。

Category.cs ファイル (ProductModel.tt の下に入れ子になっています) を開くと、Products コレクションの type が**System.collections.objectmodel.observablecollection &lt; Product &gt; **であることがわかります。

プロジェクトをコンパイルします。

## <a name="lazy-loading"></a>遅延読み込み

**Product**クラスの**Category**クラスおよび**category**プロパティの**Products**プロパティは、ナビゲーションプロパティです。 Entity Framework では、ナビゲーションプロパティを使用して、2つのエンティティ型間のリレーションシップをナビゲートすることができます。

EF では、ナビゲーションプロパティに初めてアクセスするときに、関連エンティティをデータベースから自動的に読み込むことができます。 この種類の読み込み (遅延読み込みと呼ばれます) を使用すると、各ナビゲーションプロパティに初めてアクセスしたときに、コンテンツがコンテキスト内に存在しない場合、データベースに対して個別のクエリが実行されることに注意してください。

POCO エンティティ型を使用する場合、EF は、実行時に派生プロキシ型のインスタンスを作成し、クラスの仮想プロパティをオーバーライドして読み込みフックを追加することにより、遅延読み込みを実現します。 関連オブジェクトの遅延読み込みを行うには、ナビゲーションプロパティ getter を**public**および**virtual** (Visual Basic で**オーバーライド**可能) として宣言し、クラスを**sealed** (Visual Basic では**NotOverridable** ) にする必要があります。 Database First を使用すると、遅延読み込みを有効にするために、ナビゲーションプロパティが自動的に仮想化されます。 Code First セクションでは、ナビゲーションプロパティを同じ理由で仮想にすることを選択しました。

## <a name="bind-object-to-controls"></a>コントロールへのオブジェクトのバインド

モデルで定義されているクラスを、この WPF アプリケーションのデータソースとして追加します。

-   ソリューションエクスプローラーで Mainwindow.xaml をダブルクリックしてメインフォームを開き**ます。**
-   メインメニューから、[**プロジェクト- &gt; 新しいデータソースの追加**] を選択します。
    (Visual Studio 2010 では、[**データ- &gt; 新しいデータソースの追加**] を選択する必要があります)。
-   [データソースの選択] ウィンドウで、[**オブジェクト**] を選択し、[**次へ**] をクリックします。
-   [データオブジェクトの選択] ダイアログボックスで、 **WPFwithEFSample**を2回展開し、   [**カテゴリ**] を選択します。  
    ***Product**データソースを選択する必要はありません。これは、**カテゴリ**データソースで**製品**のプロパティを使用して取得するからです。*  

    ![データオブジェクトの選択](~/ef6/media/selectdataobjects.png)

-   **[完了]** をクリックします。
-   [データソース] ウィンドウが表示されていない場合は、Mainwindow.xaml ウィンドウの横に開かれます。 [データソース] ウィンドウが表示されて*いない場合は、[表示]、[ ** &gt; その他のウィンドウ- &gt; データソース**] を選択し*ます。
-   [データソース] ウィンドウが自動的に非表示にならないように、ピンアイコンを押します。 ウィンドウが既に表示されている場合は、[更新] ボタンをクリックしなければならないことがあります。

    ![Data Sources](~/ef6/media/datasources.png)

-    **カテゴリ**データソースを選択し、フォーム上にドラッグします。

このソースをドラッグすると、次のようになります。

-   **カテゴリ Viewsource**リソースと**カテゴリ DATAGRID**コントロールが XAML に追加されました 
-   親 Grid 要素の DataContext プロパティが "{StaticResource**カテゴリ Viewsource** }" に設定されました。**カテゴリ Viewsource**リソースは、外側の親 Grid 要素のバインドソースとして機能し \\ ます。 その後、内側の Grid 要素は、親グリッドから DataContext 値を継承します (カテゴリの Datagrid の ItemsSource プロパティは "{Binding}" に設定されています)。

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

## <a name="adding-a-details-grid"></a>詳細グリッドの追加

カテゴリを表示するグリッドが用意できたので、[詳細] グリッドを追加して、関連付けられている製品を表示します。

-    **Category**データソースの [ **Products** ] プロパティを選択し、フォームにドラッグします。
    -   **カテゴリ Productviewsource**リソースと**PRODUCTDATAGRID** grid が XAML に追加されます。
    -   このリソースのバインドパスは Products に設定されています
    -   WPF データバインディングフレームワークにより、選択したカテゴリに関連する製品のみが**Productdatagrid**に表示されるようになります。
-   ツールボックスから、**ボタン**をフォームにドラッグします。 **Name**プロパティを**buttonsave**に設定し、 **Content**プロパティを**save**に設定します。

フォームは次のようになります。

![Designer](~/ef6/media/designer.png) 

## <a name="add-code-that-handles-data-interaction"></a>データの相互作用を処理するコードを追加する

ここでは、いくつかのイベントハンドラーをメインウィンドウに追加します。

-   XAML ウィンドウで** &lt; ウィンドウ**要素をクリックすると、メインウィンドウが選択されます。
-   [**プロパティ**] ウィンドウの右上にある [**イベント**] をクリックし、**読み込ま**れたラベルの右にあるテキストボックスをダブルクリックします。

    ![メインウィンドウのプロパティ](~/ef6/media/mainwindowproperties.png)

-   また、デザイナーの [保存] ボタンをダブルクリックして、[**保存**] ボタンの**click**イベントを追加します。 

これにより、フォームの分離コードが表示されます。 ProductContext を使用してデータアクセスを実行するようにコードを編集します。 次に示すように、Mainwindow.xaml のコードを更新します。

このコードは、 **Productcontext**の実行時間の長いインスタンスを宣言します。 **Productcontext**オブジェクトは、データをクエリしてデータベースに保存するために使用されます。 その後、 **Productcontext**インスタンスの**Dispose ()** が、オーバーライドされた**onclosing**メソッドから呼び出されます。コードのコメントには、コードの動作についての詳細が記載されています。

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

## <a name="test-the-wpf-application"></a>WPF アプリケーションのテスト

-   アプリケーションをコンパイルして実行します。 Code First を使用した場合は、 **WPFwithEFSample**データベースが作成されていることがわかります。
-   プライマリキーがデータベースによって生成されるため、上部のグリッドにカテゴリ名を入力し、下部のグリッドに製品名を入力して*も、ID 列には何も入力されません*。

    ![新しいカテゴリと製品を含むメインウィンドウ](~/ef6/media/screen1.png)

-   [**保存**] ボタンをクリックして、データをデータベースに保存します。

DbContext の**SaveChanges ()** を呼び出した後、id にはデータベースで生成された値が設定されます。 **SaveChanges ()** 後に**Refresh ()** を呼び出したため、 **DataGrid**コントロールも新しい値で更新されます。

![Id が設定されたメインウィンドウ](~/ef6/media/screen2.png)

## <a name="additional-resources"></a>その他のリソース

WPF を使用したコレクションへのデータバインディングの詳細については、WPF のドキュメントの[このトピック](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections)を参照してください。  
