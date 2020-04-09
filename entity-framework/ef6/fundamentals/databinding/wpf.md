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
> **このドキュメントは、.NET Framework の WPF でのみ有効です。**
>
> このドキュメントでは、.NET Framework での WPF のデータバインディングについて説明します。 新しい .NET Core プロジェクトの場合は、エンティティ フレームワーク 6 ではなく[EF Core](/ef/core)を使用することをお勧めします。 EF Core でのデータバインディングのドキュメントは、[問題#778](https://github.com/dotnet/EntityFramework.Docs/issues/778)で追跡されています。

# <a name="databinding-with-wpf"></a>WPF とのデータバインド
この手順のチュートリアルでは、"マスター詳細" フォームで POCO 型を WPF コントロールにバインドする方法を示します。 アプリケーションは、Entity Framework API を使用して、データベースからのデータをオブジェクトに格納し、変更を追跡し、データベースにデータを永続化します。

モデルは、**一**対多の関係に参加する 2 つのタイプを定義\\します: カテゴリ (\\主体マスター) と**製品**(従属詳細)。 次に、Visual Studio ツールを使用して、モデルで定義されている型を WPF コントロールにバインドします。 WPF データ バインディング フレームワークを使用すると、関連するオブジェクト間のナビゲーションが可能になり、マスター ビューで行を選択すると、詳細ビューが対応する子データで更新されます。

このチュートリアルのスクリーン ショットとコード一覧は、Visual Studio 2013 から取得されますが、このチュートリアルは Visual Studio 2012 または Visual Studio 2010 で完了できます。

## <a name="use-the-object-option-for-creating-wpf-data-sources"></a>WPF データ ソースを作成するための 'オブジェクト' オプションを使用します。

以前のバージョンの Entity Framework では、EF デザイナーで作成されたモデルに基づいて新しいデータ ソースを作成するときに **、データベース**オプションを使用することをお勧めしていました。 これは、デザイナーが、ObjectContext から派生したコンテキストと、EntityObject から派生したエンティティ クラスを生成するためです。 データベース オプションを使用すると、この API サーフェスと対話するための最適なコードを記述できます。

2012 年と Visual Studio 2013 の EF デザイナーは、単純な POCO エンティティ クラスと共に DbContext から派生したコンテキストを生成します。 Visual Studio 2010 では、このチュートリアルで後述するように、DbContext を使用するコード生成テンプレートにスワップすることをお勧めします。

DbContext API サーフェスを使用する場合は、このチュートリアルに示すように、新しいデータ ソースを作成するときに**オブジェクト**オプションを使用する必要があります。

必要に応じて、EF デザイナーで作成されたモデルの[ObjectContext ベースのコード生成に戻](~/ef6/modeling/designer/codegen/legacy-objectcontext.md)すことができます。

## <a name="pre-requisites"></a>前提条件

このチュートリアルを完了するには、Visual Studio 2013、Visual Studio 2012 または Visual Studio 2010 がインストールされている必要があります。

Visual Studio 2010 を使用している場合は、NuGet をインストールする必要もあります。 詳細については、「 [NuGet](https://docs.microsoft.com/nuget/install-nuget-client-tools)のインストール 」を参照してください。  

## <a name="create-the-application"></a>アプリケーションを作成する

-   Visual Studio を開きます
-   **ファイル&gt;-&gt;新規 - プロジェクト.**
-   左側のペインで **[ウィンドウ** ] を選択し、右側のペインで **[WPF アプリケーション**] を選択します。
-    名前 **として入力します。**
-    **OK を選択します。**

## <a name="install-the-entity-framework-nuget-package"></a>エンティティ フレームワーク NuGet パッケージをインストールします。

-   ソリューション エクスプローラーで **、WinFormswithEFSample**プロジェクトを右クリックします。
-   **[NuGet パッケージの管理] を選択します。**
-   [NuGet パッケージの管理] ダイアログで、[**オンライン**] タブを選択し **、EntityFramework**パッケージを選択します。
-   [インストール] をクリック**します。**  
    >[!NOTE]
    > エンティティフレームワーク アセンブリに加えて、システム.コンポーネントモデルへの参照も追加されます。 プロジェクトに System.Data.Entity への参照がある場合、EntityFramework パッケージのインストール時に削除されます。 エンティティ フレームワーク 6 アプリケーションでは、System.Data.Entity アセンブリは使用されなくなりました。

## <a name="define-a-model"></a>モデルの定義

このチュートリアルでは、コード ファーストまたは EF デザイナーを使用してモデルを実装できます。 次の 2 つのセクションのいずれかを実行します。

### <a name="option-1-define-a-model-using-code-first"></a>方法 1: コードを使用してモデルを定義する

このセクションでは、Code First を使用してモデルと関連付けられたデータベースを作成する方法を示します。 データベース ファーストを使用して EF デザイナを使用してデータベースからモデルをリバース エンジニアリングする場合は、次のセクションに進んでください (「**オプション 2: データベースファーストを使用してモデルを定義する」)。**

コードファースト開発を使用する場合は、概念 (ドメイン) モデルを定義する .NET Framework クラスを記述します。

-   新しいクラスを **WPFwithEF サンプルに追加します。**
    -   プロジェクト名を右クリックします。
    -   [**追加**] を選択し、[**新しいアイテム] を選択します。**
    -   **[クラス] を**選択し、クラス名に **製品** を入力します。
-    **Product** クラス定義を次のコードに置き換えます。

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

**Product**クラスの**カテゴリ**クラスと**カテゴリ**プロパティの**Products**プロパティは、ナビゲーション プロパティです。 Entity Framework では、ナビゲーション プロパティは、2 つのエンティティ型間のリレーションシップをナビゲートする方法を提供します。

エンティティの定義に加えて、DbContext から派生し、DbSet&lt;TEntity プロパティを公開するクラスを&gt;定義する必要があります。 DbSet&lt;TEntity&gt;プロパティは、モデルに含める型をコンテキストに知らせます。

DbContext 派生型のインスタンスは、実行時にエンティティ オブジェクトを管理します。

-   次の定義を使用して、新しい**ProductContext**クラスをプロジェクトに追加します。

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

### <a name="option-2-define-a-model-using-database-first"></a>オプション 2: データベースを使用してモデルを定義する

このセクションでは、データベース ファーストを使用して、EF デザイナを使用してデータベースからモデルをリバース エンジニアリングする方法を示します。 前のセクション (オプション**1: Code First を使用してモデルを定義する)** を完了した場合は、このセクションをスキップして **、遅延読み込み**セクションに進みます。

#### <a name="create-an-existing-database"></a>既存のデータベースを作成する

通常、既存のデータベースをターゲットにしている場合は既に作成されていますが、このチュートリアルでは、アクセスするデータベースを作成する必要があります。

Visual Studio と共にインストールされるデータベース サーバーは、インストールされている Visual Studio のバージョンによって異なります。

-   Visual Studio 2010 を使用している場合は、SQL エクスプレス データベースを作成します。
-   Visual Studio 2012 を使用している場合は[、LocalDB](https://msdn.microsoft.com/library/hh510202.aspx)データベースを作成します。

それでは、データベースを生成してみましょう。

-   **表示&gt;- サーバー エクスプローラ**
-   **[データ接続&gt;- 接続の追加]を右クリックします。**
-   サーバー エクスプローラーからデータベースに接続していない場合は、データ ソースとして SQL Server を選択する必要がある場合

    ![データ ソースの変更](~/ef6/media/changedatasource.png)

-   インストールしたローカルまたは SQL Express に接続し、データベース名として **「製品」** と入力します。

    ![接続ローカルデータベースの追加](~/ef6/media/addconnectionlocaldb.png)

    ![接続の追加 Express](~/ef6/media/addconnectionexpress.png)

-   **[OK] を**選択すると、新しいデータベースを作成するかどうかの確認が求**められます。**

    ![データベースの作成](~/ef6/media/createdatabase.png)

-   新しいデータベースがサーバー エクスプローラに表示され、右クリックして **[新しいクエリ**] を選択します。
-   次の SQL を新しいクエリにコピーし、クエリを右クリックして[**実行**]を選択します。

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

#### <a name="reverse-engineer-model"></a>リバース エンジニアリング モデル

このモデルを作成するために、Visual Studio の一部として含まれているエンティティ フレームワーク デザイナーを使用します。

-   **プロジェクト&gt;- 新しい項目の追加.**
-   左側のメニューから **[データ**] を選択し、**エンティティ データ モデルADO.NET**
-   **名前として製品モデル**を入力し **、[OK]** をクリックします。
-   エンティティ データ**モデル ウィザードが起動します。**
-   [**データベースから生成]を**選択し、[**次へ**]をクリックします。

    ![モデル コンテンツの選択](~/ef6/media/choosemodelcontents.png)

-   最初のセクションで作成したデータベースへの接続を選択し、接続文字列の名前として**ProductContext**と入力し、[**次へ**] をクリックします。

    ![接続を選択する](~/ef6/media/chooseyourconnection.png)

-   すべてのテーブルをインポートするには、[テーブル] の横にあるチェックボックスをクリックし、[完了] をクリックします。

    ![オブジェクトを選択する](~/ef6/media/chooseyourobjects.png)

リバース エンジニアリング プロセスが完了すると、新しいモデルがプロジェクトに追加され、エンティティ フレームワーク デザイナーで表示できます。 App.config ファイルも、データベースの接続の詳細を含むプロジェクトに追加されました。

#### <a name="additional-steps-in-visual-studio-2010"></a>Visual Studio 2010 のその他の手順

Visual Studio 2010 で作業している場合は、EF6 コード生成を使用するように EF デザイナーを更新する必要があります。

-   EF デザイナーでモデルの空の場所を右クリックし、[**コード生成項目の追加]を選択します。**
-   左側**のメニューからオンラインテンプレート**を選択し **、DbContext**を検索します
-   **\#C 用 EF 6.x DbContext ジェネレーターを選択し、** 名前として**製品モデル**を入力し、追加 をクリックします。

#### <a name="updating-code-generation-for-data-binding"></a>データ バインディング用のコード生成の更新

EF は T4 テンプレートを使用してモデルからコードを生成します。 Visual Studio に付属のテンプレート、または Visual Studio ギャラリーからダウンロードされたテンプレートは、汎用的な用途を目的としています。 つまり、これらのテンプレートから生成されたエンティティには、単純な&lt;ICollection T&gt;プロパティがあります。 ただし、WPF を使用してデータ バインディングを実行する場合は、コレクション プロパティに**ObservableCollection**を使用して、WPF がコレクションに加えられた変更を追跡できるようにすることが望ましいです。 この目的のために、テンプレートを変更して ObservableCollection を使用します。

-   ソリューション**エクスプローラー**を開き **、ProductModel.edmx**ファイルを見つけます。
-   ProductModel.edmx ファイルの下にネストされる**ProductModel.tt**ファイルを見つけます。

    ![WPF 製品モデル テンプレート](~/ef6/media/wpfproductmodeltemplate.png)

-   ProductModel.ttファイルをダブルクリックして、Visual Studio エディターで開きます。
-   "ICollection" の 2 つの出現箇所を検索して **、"ObservableCollection"** に置き換えます。**ICollection** これらは、約 296 行目と 484 行目にあります。
-   "**HashSet**" の最初の出現箇所を検索して、"**観測可能なコレクション**" に置き換えます。 このオカレンスは、ほぼ 50 行目にあります。 コードの後半で見つかった HashSet の 2 番目のオカレンスを置き換**えないでください**。
-   "**System.Collections.Generic**" の唯一の出現を**System.Collections.ObjectModel**検索して置き換えます。 これは、約 424 行目にあります。
-   ProductModel.tt ファイルを保存します。 これにより、エンティティのコードが再生成されます。 コードが自動的に再生されない場合は、ProductModel.tt右クリックして[カスタムツールを実行]を選択します。

Category.cs ファイル (ProductModel.ttの下にネストされている) を開くと、Products コレクションの種類が **[監視可能コレクション製品&lt;&gt;**] であることがわかります。

プロジェクトをコンパイルします。

## <a name="lazy-loading"></a>遅延読み込み

**Product**クラスの**カテゴリ**クラスと**カテゴリ**プロパティの**Products**プロパティは、ナビゲーション プロパティです。 Entity Framework では、ナビゲーション プロパティは、2 つのエンティティ型間のリレーションシップをナビゲートする方法を提供します。

EF では、ナビゲーション プロパティに最初にアクセスするときに、データベースから関連エンティティを自動的に読み込むオプションが提供されます。 このタイプの読み込み (遅延読み込み) では、各ナビゲーション プロパティに初めてアクセスするときに、コンテンツがまだコンテキスト内にない場合は、データベースに対して別のクエリが実行されます。

POCO エンティティ型を使用する場合、EF は実行時に派生プロキシ型のインスタンスを作成し、クラスの仮想プロパティをオーバーライドして読み込みフックを追加することで、遅延読み込みを実現します。 関連オブジェクトの遅延読み込みを取得するには、ナビゲーション プロパティ**NotOverridable**のゲッターを**パブリック**および**仮想**(Visual Basic では**オーバーライド可能**) として宣言する必要があります。 **sealed** Database First ナビゲーション プロパティを使用すると、遅延読み込みを有効にするために自動的に仮想にします。 [コード 1] セクションでは、同じ理由でナビゲーション プロパティを仮想にすることを選択しました

## <a name="bind-object-to-controls"></a>コントロールにオブジェクトをバインド

この WPF アプリケーションのデータ ソースとしてモデルで定義されているクラスを追加します。

-   ソリューション エクスプローラーで**MainWindow.xaml**をダブルクリックして、メイン フォームを開きます。
-   メイン メニューから、[**プロジェクト ]&gt; - [新しいデータ ソースの追加] を選択します。**
    (Visual Studio 2010 では、[**データ -&gt;新しいデータ ソースの追加]** を選択する必要があります。
-   [データ ソースの種類の選択] ウィンドウで、[**オブジェクト] を**選択し、[**次へ**] をクリックします。
-   [データ オブジェクトの選択] ダイアログで **、WPFwithEFSample** を 2 回展開し、[**カテゴリ**]  
    ***商品**データ ソースを選択する**Product**必要はありません。 **Product***  

    ![データ オブジェクトの選択](~/ef6/media/selectdataobjects.png)

-   **[完了]** をクリックします。
-   [データ ソース] ウィンドウが [MainWindow.xaml] ウィンドウの横に表示*される [データ ソース] ウィンドウが表示されていない場合は、[**表示 -&gt;その他の Windows-&gt;データ ソース**] を選択します。*
-   ピン アイコンを押すと、[データ ソース] ウィンドウが自動的に非表示になっていません。 ウィンドウが既に表示されている場合は、更新ボタンを押す必要があります。

    ![ソリューション エクスプローラー](~/ef6/media/datasources.png)

-    **[カテゴリ]** データ ソースを選択し、フォーム上にドラッグします。

このソースをドラッグすると、次のことが起こりました。

-   **リソース**と**カテゴリデータ グリッド**コントロールが XAML に追加されました。 
-   親グリッド要素の DataContext プロパティが "{静的リソース**カテゴリビューソース**} に設定されました。**categoryViewSource**リソースは、外部\\の親グリッド要素のバインディング ソースとして機能します。 内部グリッド要素は、親グリッドから DataContext 値を継承します (カテゴリデータグリッドの ItemsSource プロパティが "{バインディング}" に設定されています)。

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

カテゴリを表示するグリッドができたので、関連する製品を表示するための詳細グリッドを追加します。

-   [商品] **プロパティを** **[カテゴリ]** データ ソースの下から選択し、フォーム上にドラッグします。
    -   **カテゴリ製品ビューソース**リソースと**製品データ グリッド**が XAML に追加されます。
    -   このリソースのバインド パスは[製品]に設定されています。
    -   WPF データ バインディング フレームワークは、選択したカテゴリに関連する製品のみが**ProductDataGrid**に表示されるようにします。
-   ツールボックスから、フォームに**ボタン**をドラッグします。 **Name**プロパティを**ボタンに設定し、****コンテンツ**プロパティを [ 保存 ] に**設定**します。

フォームは次のようになります。

![Designer](~/ef6/media/designer.png) 

## <a name="add-code-that-handles-data-interaction"></a>データの相互作用を処理するコードを追加する

メイン ウィンドウにイベント ハンドラーを追加します。

-   XAML ウィンドウで、**&lt;ウィンドウ**要素をクリックすると、メイン ウィンドウが選択されます。
-   [**プロパティ]** ウィンドウの右上にある **[イベント**] を選択し、[**読み込み]** ラベルの右にあるテキスト ボックスをダブルクリックします。

    ![メイン ウィンドウのプロパティ](~/ef6/media/mainwindowproperties.png)

-   また、デザイナーの **[保存**] ボタンをダブルクリックして、[保存] ボタンの**Click**イベントを追加します。 

これにより、フォームの分離コードが表示され、コードを編集して ProductContext を使用してデータ アクセスを実行します。 次に示すように、メイン ウィンドウのコードを更新します。

このコードでは、実行時間の長いインスタンス**を宣言**します。 **オブジェクト**は、クエリを実行し、データベースにデータを保存するために使用されます。 その後、オーバーライドされた**OnClosing**メソッドから、**インスタンス**の**Dispose()** が呼び出されます。コード コメントは、コードの動作に関する詳細を示します。

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

-   アプリケーションをコンパイルして実行します。 コードファーストを使用した場合は **、WPFwithEFSample.ProductContext**データベースが作成されていることがわかります。
-   一番上のグリッドにカテゴリ名を入力し、下部のグリッドに商品名を*入力します ID 列には何も入力しないでください。*

    ![新しいカテゴリと製品を含むメインウィンドウ](~/ef6/media/screen1.png)

-   **[保存**] ボタンを押して、データをデータベースに保存します。

呼び出しの**後に、** データベースで生成された値が ID に設定されます。 **SaveChanges()** の後**に Refresh()** を呼び出したので **、DataGrid**コントロールも新しい値で更新されます。

![ID が設定されたメイン ウィンドウ](~/ef6/media/screen2.png)

## <a name="additional-resources"></a>その他のリソース

WPF を使用したコレクションへのデータ バインディングの詳細については、WPF のドキュメントの[このトピック](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections)を参照してください。  
