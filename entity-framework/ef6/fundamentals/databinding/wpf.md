---
title: WPF の EF6 とのデータ バインド
author: divega
ms.date: 10/23/2016
ms.assetid: e90d48e6-bea5-47ef-b756-7b89cce4daf0
ms.openlocfilehash: 1933988277d3be8fecc02fced3293f2b7f80c901
ms.sourcegitcommit: ae399f9f3d1bae2c446b552247bd3af3ca5a2cf9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2018
ms.locfileid: "48575666"
---
# <a name="databinding-with-wpf"></a>WPF とのデータ バインド
このステップ バイ ステップ チュートリアルでは、POCO 型を「マスター/詳細」の形式での WPF コントロールにバインドする方法を示します。 アプリケーションでは、Entity Framework の Api を使用して、データベースからデータをオブジェクトに設定、変更の追跡、およびデータベースにデータを保持します。

モデルが一対多のリレーションシップに参加している 2 つの型を定義します。**カテゴリ**(プリンシパル\\マスター) と**製品**(依存\\詳細)。 次に、Visual Studio ツールは、WPF コントロール モデルで定義された型のバインドに使用されます。 WPF データ バインディング フレームワークにより、関連するオブジェクト間のナビゲーション: により、対応する子データで更新する詳細ビューがマスター ビューの行を選択します。

スクリーン ショットとこのチュートリアルで示した各コードは、Visual Studio 2013 から取得されますが、このチュートリアルでは、Visual Studio 2012 または Visual Studio 2010 を行うことができます。

## <a name="use-the-object-option-for-creating-wpf-data-sources"></a>WPF のデータ ソースを作成するため、'Object' オプションを使用します。

Entity Framework の以前のバージョンを使用して推奨を使用して、**データベース**EF Designer で作成したモデルに基づいて新しいデータ ソースを作成するときのオプションします。 これは、ObjectContext から派生したコンテキストとエンティティ クラスを EntityObject から派生したデザイナーが生成するためでした。 データベース オプションを使用してこの API サーフェスと対話するための最適なコードを記述してくれます。

Visual Studio 2012 および Visual Studio 2013 用の EF デザイナーでは、単純な POCO エンティティ クラスと DbContext から派生したコンテキストを生成します。 Visual Studio 2010 で、このチュートリアルの後半で説明したように DbContext を使用するコード生成テンプレートにスワップをお勧めします。

使用する必要があります、DbContext API サーフェスを使用する場合、**オブジェクト**このチュートリアルで示すように、新しいデータ ソースを作成するときのオプションします。

場合は、必要な[ObjectContext に基づくコードの生成に戻す](~/ef6/modeling/designer/codegen/legacy-objectcontext.md)EF Designer で作成されたモデル。

## <a name="pre-requisites"></a>前提条件

Visual Studio 2013 を持っている必要があります、このチュートリアルを完了する、Visual Studio 2012 または Visual Studio 2010 がインストールされています。

Visual Studio 2010 を使用している場合は、NuGet をインストールする必要があります。 詳細については、[をインストールする NuGet](https://docs.microsoft.com/nuget/install-nuget-client-tools)を参照してください。  

## <a name="create-the-application"></a>アプリケーションを作成する

-   Visual Studio を開く
-   **ファイル -&gt;新機能 -&gt;プロジェクト.**
-   選択**Windows**左側のウィンドウで、 **WPFApplication**右側のウィンドウで
-   入力**WPFwithEFSample**名として
-   **[OK]** を選択します。

## <a name="install-the-entity-framework-nuget-package"></a>Entity Framework NuGet パッケージをインストールします。

-   ソリューション エクスプ ローラーを右クリックし、 **WinFormswithEFSample**プロジェクト
-   選択**NuGet パッケージを管理しています.**
-   NuGet パッケージの管理 ダイアログ ボックスで、、**オンライン** タブで選択し、 **EntityFramework**パッケージ
-   クリックして**インストール**  
    >[!NOTE]
    > EntityFramework アセンブリだけでなく System.ComponentModel.DataAnnotations への参照も追加されます。 System.Data.Entity への参照をプロジェクトには場合、は、EntityFramework パッケージがインストールされている場合、削除するされます。 System.Data.Entity アセンブリは Entity Framework 6 アプリケーションは使用されません。

## <a name="define-a-model"></a>モデルを定義します。

できます、このチュートリアルでは、Code First または EF Designer を使用してモデルを実装するために選択しました。 2 つのセクションでは、次のいずれかを完了します。

### <a name="option-1-define-a-model-using-code-first"></a>オプション 1: Code First を使用してモデルを定義します。

このセクションでは、モデルおよび関連する Code First を使用してデータベースを作成する方法を示します。 次のセクションにスキップ (**オプション 2: Database First を使用してモデルを定義)** エンジニア リング EF designer を使用してデータベースからモデルを使用する場合は、データベースの最初を逆にします。

Code First の開発を使用する場合に、(ドメイン) の概念モデルを定義する .NET Framework クラスを記述することで、通常で開始します。

-   新しいクラスを追加、 **WPFwithEFSample:**
    -   プロジェクト名を右クリックします。
    -   選択**追加**、し**新しい項目**
    -   選択**クラス**入力**製品**クラス名
-   置換、**製品**クラス定義を次のコード。

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

**製品**プロパティを**カテゴリ**クラスと**カテゴリ**プロパティを**製品**クラスは、ナビゲーション プロパティ。 Entity Framework では、ナビゲーション プロパティは、2 つのエンティティ型間のリレーションシップを移動する方法を提供します。

エンティティを定義するだけでなくを DbContext から派生し、DbSet を公開するクラスを定義する必要があります。&lt;TEntity&gt;プロパティ。 DbSet&lt;TEntity&gt;プロパティは、モデルに追加する型も把握してコンテキストを使用します。

DbContext 派生型のインスタンスがデータベースからデータを使用してオブジェクトの設定が含まれています。 ランタイム処理中に、エンティティ オブジェクトを管理、追跡、および永続化するデータをデータベースに変更します。

-   新しい追加**ProductContext**クラスをプロジェクトに次の定義。

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

### <a name="option-2-define-a-model-using-database-first"></a>オプション 2: Database First を使用してモデルを定義します。

このセクションでは、EF designer を使用してデータベースからモデルをリバース エンジニア リング Database First を使用する方法を示します。 前のセクションを完了している場合 (**オプション 1: Code First を使用してモデルを定義)**、このセクションをスキップし、すぐに進んで、 **Lazy Loading**セクション。

#### <a name="create-an-existing-database"></a>既存のデータベースを作成します。

通常それは既に作成されて、既存のデータベースを対象としているときに、このチュートリアルではアクセスするデータベースを作成する必要があります。

Visual Studio と共にインストールされているデータベース サーバーは、インストールした Visual Studio のバージョンによって異なります。

-   Visual Studio 2010 を使用している場合は、SQL Express データベースを作成するがします。
-   Visual Studio 2012 を使用しているかどうかは、作成する、 [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx)データベース。

データベースを生成してみましょう。

-   **ビュー -&gt;サーバー エクスプ ローラー**
-   右クリックして**データ接続 -&gt;接続の追加.**
-   場合、まだデータベースに接続して、サーバー エクスプ ローラーから前に、データ ソースとして Microsoft SQL Server を選択する必要があります。

    ![データ ソースの変更](~/ef6/media/changedatasource.png)

-   LocalDB または SQL Express をインストールしたものに応じてのいずれかに接続し、入力**製品**データベース名として

    ![LocalDB の接続を追加します。](~/ef6/media/addconnectionlocaldb.png)

    ![接続の高速を追加します。](~/ef6/media/addconnectionexpress.png)

-   選択**OK**かどうかは、新しいデータベースを作成するように要求がある **[はい]**

    ![データベースの作成](~/ef6/media/createdatabase.png)

-   新しいデータベースがサーバー エクスプ ローラーに表示されますを右クリックして選択**新しいクエリ**
-   新しいクエリに、次の SQL をコピーし、クエリを選択します右クリックし、 **Execute**

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

#### <a name="reverse-engineer-model"></a>リバース エンジニア リング モデル

Visual Studio の一部として含まれている Entity Framework デザイナーを使用してモデルを作成することになります。

-   **プロジェクト -&gt;新しい項目を追加しています.**
-   選択**データ**左側のメニューをクリックし**ADO.NET Entity Data Model**
-   入力**ProductModel**名をクリックします**OK**
-   これにより、起動、 **Entity Data Model ウィザード**
-   選択**データベースから生成**クリック **[次へ]**

    ![モデル コンテンツの選択](~/ef6/media/choosemodelcontents.png)

-   最初のセクションで作成したデータベース接続を選択して、入力**ProductContext**をクリックして、接続文字列の名前として **[次へ]**

    ![接続を選択します。](~/ef6/media/chooseyourconnection.png)

-   すべてのテーブルをインポートし、[完了] をクリックします。 'テーブル' の横にあるチェック ボックスをクリックします。

    ![オブジェクトを選択します。](~/ef6/media/chooseyourobjects.png)

リバース エンジニア リング プロセスが完了すると、新しいモデルがプロジェクトに追加しを開いて、Entity Framework デザイナーで表示することです。 App.config ファイルが、データベースの接続詳細を含むプロジェクトにも追加されました。

#### <a name="additional-steps-in-visual-studio-2010"></a>Visual Studio 2010 で追加の手順

Visual Studio 2010 で作業している場合は、EF6 コード生成を使用する EF designer を更新する必要があります。

-   EF Designer でのモデルの空いている場所を右クリックして**コード生成項目の追加.**
-   選択**オンライン テンプレート**、左側のメニューと検索から**DbContext**
-   選択、 **EF 6.x C 用 DbContext ジェネレーター\#、** 入力**ProductsModel**名として追加 をクリック

#### <a name="updating-code-generation-for-data-binding"></a>データ バインディングの更新のコード生成

EF では、T4 テンプレートを使用して、モデルからコードを生成します。 Visual Studio に付属のか、Visual Studio ギャラリーからダウンロードしたテンプレートは、一般的な用途に使用されるもの。 つまり、これらのテンプレートから生成されたエンティティがある単純な ICollection&lt;T&gt;プロパティ。 ただし、データを実施する際に WPF を使用したバインドが使用することが望ましい**ObservableCollection**コレクションのプロパティを WPF の追跡できる、コレクションに加えられた変更のためにします。 我々 はこれに対応する ObservableCollection を使用するテンプレートを変更します。

-   開く、**ソリューション エクスプ ローラー**を見つけて**ProductModel.edmx**ファイル
-   検索、 **ProductModel.tt** ProductModel.edmx ファイルの下で入れ子にはファイル

    ![WPF 製品モデルのテンプレート](~/ef6/media/wpfproductmodeltemplate.png)

-   Visual Studio エディターで開く ProductModel.tt ファイルをダブルクリックします
-   検索し、置換の 2 つの出現回数"**ICollection**"with"**ObservableCollection**"。 これらは、約 296 および 484 行にあります。
-   検索し、置換の最初に見つかった"**HashSet**"with"**ObservableCollection**"。 この状況の発生が約 50 行に配置されています。 **しない**HashSet は後で、コードの 2 番目に出現を置換します。
-   検索し、置換のみ出現"**System.Collections.Generic**"with"**System.Collections.ObjectModel**"。 これは約 424 の行にあります。
-   ProductModel.tt ファイルを保存します。 これは、再生成するエンティティのコードで発生する必要があります。 コードは自動的に再生成しない場合は、ProductModel.tt を右クリックし、「カスタム ツールの実行」を選択します。

かどうか (これは ProductModel.tt 下で入れ子になった) Category.cs ファイルを開くようになりましたし、製品のコレクションが、型を持つことを確認する必要があります**ObservableCollection&lt;製品&gt;** します。

プロジェクトをコンパイルします。

## <a name="lazy-loading"></a>遅延読み込み

**製品**プロパティを**カテゴリ**クラスと**カテゴリ**プロパティを**製品**クラスは、ナビゲーション プロパティ。 Entity Framework では、ナビゲーション プロパティは、2 つのエンティティ型間のリレーションシップを移動する方法を提供します。

EF では、関連エンティティを読み込む、データベースから自動的に初めてナビゲーション プロパティにアクセスするの選ぶことができます。 この種類の読み込み (遅延読み込みと呼ばれます) にするには、初めての各ナビゲーション プロパティにアクセスする別のクエリが実行されること、データベースに対して、内容が既にコンテキストでない場合あります。

POCO エンティティ型を使用する場合、EF は実行時にプロキシの派生型のインスタンスを作成し、読み込み用のフックを追加するクラスの仮想プロパティをオーバーライドして遅延読み込みを実現します。 関連オブジェクトの遅延読み込みを取得する必要がありますを宣言するナビゲーション プロパティの getter として**パブリック**と**仮想**(**Overridable** Visual Basic で)、およびするクラスがすることはできません**シール**(**NotOverridable** Visual Basic で)。 データベースを使用して最初のナビゲーション プロパティは自動的遅延読み込みを有効にするための仮想になります。 コードの最初のセクションで同じ理由から仮想ナビゲーション プロパティを作成することにしました

## <a name="bind-object-to-controls"></a>オブジェクトをコントロールにバインドします。

この WPF アプリケーションのデータ ソースとしてモデルに定義されているクラスを追加します。

-   ダブルクリック**MainWindow.xaml**メイン フォームを開くためのソリューション エクスプ ローラー
-   メイン メニューで、次のように選択します**プロジェクト -&gt;新しいデータ ソースを追加しています...**
    (Visual Studio 2010 では、選択する必要があります**データ -&gt;新しいデータ ソースの追加...**)
-   データ ソースの Typewindow 選択で、次のように選択します**オブジェクト**クリック**次へ。**
-   選択、データ オブジェクト ダイアログ ボックスで、展開、 **WPFwithEFSample** 2 つの時刻と選択**カテゴリ**  
    *選択する必要はありません、**製品**を通じていたしますので、データ ソース、**製品**のプロパティを**カテゴリ**データ ソース*  

    ![データ オブジェクトを選択します。](~/ef6/media/selectdataobjects.png)

-   クリックして**完了します。**
-   MainWindow.xaml のウィンドウの横にあるデータ ソース ウィンドウが開かれる*データ ソース ウィンドウが表示されていない場合は、選択**ビュー -&gt;その他の Windows-&gt;データ ソース***
-   キーを押して、データ ソース ウィンドウが自動しないように、ピン アイコンを非表示にします。 ウィンドウが表示された場合は、更新ボタンをクリックする必要があります。

    ![Data Sources](~/ef6/media/datasources.png)

-   選択、**カテゴリ**データ ソースをフォームにドラッグします。

このソースがドラッグされると、次のメッセージが発生しました。

-   **CategoryViewSource**リソースと**categoryDataGrid**コントロールが XAML に追加されました。 
-   親グリッド要素の DataContext プロパティに設定されました"{StaticResource **categoryViewSource** }"。 **CategoryViewSource**リソースは、外側のバインディング ソースとして機能\\親グリッド要素。 内部のグリッド要素は、親 (categoryDataGrid の ItemsSource プロパティは"{binding}"に設定) をグリッドから DataContext 値を継承する、

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

## <a name="adding-a-details-grid"></a>詳細グリッドを追加します。

あるので、みましょうカテゴリを表示するグリッドは、関連付けられている製品を表示する詳細グリッドを追加します。

-   選択、**製品**プロパティの下にある、**カテゴリ**データ ソースをフォームにドラッグします。
    -   **CategoryProductsViewSource**リソースと**productDataGrid**グリッドが XAML に追加されます
    -   製品には、このリソースのバインド パスを設定してください。
    -   WPF データ バインディング フレームワークでは、製品だけが選択したカテゴリに関連する表示ことにより、 **productDataGrid**
-   ツールボックスからドラッグ**ボタン**フォームにログオンします。 設定、**名前**プロパティを**buttonSave**と**コンテンツ**プロパティを**保存**します。

フォームは、次のようになります。

![Designer](~/ef6/media/designer.png) 

## <a name="add-code-that-handles-data-interaction"></a>データの対話を処理するコードを追加します。

メイン ウィンドウにいくつかのイベント ハンドラーを追加する時間になります。

-   XAML ウィンドウで、クリックして、 **&lt;ウィンドウ**要素、これは、メイン ウィンドウを選択します。
-   **プロパティ**ウィンドウ選択**イベント**右側の上部にあるテキスト ボックスの右側をダブルクリックし、 **Loaded**ラベル

    ![メイン ウィンドウのプロパティ](~/ef6/media/mainwindowproperties.png)

-   追加も、  **をクリックして**イベントを**保存**デザイナーの [Save] ボタンをダブルクリックしてボタンをクリックします。 

分離コードをフォームの表示、ProductContext を使用して、データ アクセスを実行するコードを編集しますようになりました。 次に示すように、MainWindow のコードを更新します。

コードの実行時間の長いインスタンス**ProductContext**します。 **ProductContext**オブジェクトを照会し、データベースにデータを保存に使用されます。 **Dispose()** 上、 **ProductContext**インスタンスが呼び出され、オーバーライドされたから**OnClosing**メソッド。 コード コメントでは、コードの動作について詳しく説明します。

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

## <a name="test-the-wpf-application"></a>WPF アプリケーションをテストします。

-   アプリケーションをコンパイルして実行します。 Code First を使用したかどうかは、ことがわかりますが、 **WPFwithEFSample.ProductContext**データベースが作成されます。
-   下部のグリッドに上位のグリッドと製品の名前でカテゴリ名を入力*は何も入力しない ID の列に主キーがデータベースによって生成されるため、*

    ![新しいカテゴリおよび製品とのメイン ウィンドウ](~/ef6/media/screen1.png)

-   キーを押して、**保存**データベースにデータを保存するボタンをクリックします。

DbContext の呼び出しの後**SaveChanges()** データベースで生成された値は、Id が格納されます。 呼び出したため**Refresh()** 後**SaveChanges()** 、 **DataGrid**コントロールも、新しい値で更新します。

![Id が設定されますが、メイン ウィンドウ](~/ef6/media/screen2.png)

## <a name="additional-resources"></a>その他のリソース

WPF を使用してコレクションへのデータ バインディングの詳細については、次を参照してください。[このトピックの「](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections) WPF のドキュメント。  
