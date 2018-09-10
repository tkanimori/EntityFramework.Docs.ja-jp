---
title: WinForms - EF6 とのデータ バインド
author: divega
ms.date: 2016-10-23
ms.assetid: 80fc5062-2f1c-4dbd-ab6e-b99496784b36
ms.openlocfilehash: 071172810f7dac45f42aca0efa7f329bac31e9cd
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2018
ms.locfileid: "44251194"
---
# <a name="databinding-with-winforms"></a>WinForms とのデータ バインド
このステップ バイ ステップ チュートリアルでは、POCO 型を「マスター/詳細」の形式で、ウィンドウ フォーム (WinForms) コントロールにバインドする方法を示します。 アプリケーションでは、Entity Framework を使用して、データベースからデータをオブジェクトに設定、変更の追跡、およびデータベースにデータを保持します。

モデルが一対多のリレーションシップに参加している 2 つの型を定義します。 カテゴリ (プリンシパル\\マスター) と製品 (依存\\詳細)。 次に、Visual Studio ツールは、WinForms コントロールのモデルで定義された型のバインドに使用されます。 WinForms データ バインディング フレームワークにより、関連するオブジェクト間のナビゲーション: により、対応する子データで更新する詳細ビューがマスター ビューの行を選択します。

スクリーン ショットとこのチュートリアルで示した各コードは、Visual Studio 2013 から取得されますが、このチュートリアルでは、Visual Studio 2012 または Visual Studio 2010 を行うことができます。

## <a name="pre-requisites"></a>前提条件

Visual Studio 2013 を持っている必要があります、このチュートリアルを完了する、Visual Studio 2012 または Visual Studio 2010 がインストールされています。

Visual Studio 2010 を使用している場合は、NuGet をインストールする必要があります。 詳細については、次を参照してください。[をインストールする NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)します。

## <a name="create-the-application"></a>アプリケーションを作成する

-   Visual Studio を開く
-   **ファイル -&gt;新機能 -&gt;プロジェクト.**
-   選択**Windows**左側のウィンドウで、 **Windows FormsApplication**右側のウィンドウで
-   入力**WinFormswithEFSample**名として
-   **[OK]** を選択します。

## <a name="install-the-entity-framework-nuget-package"></a>Entity Framework NuGet パッケージをインストールします。

-   ソリューション エクスプ ローラーを右クリックし、 **WinFormswithEFSample**プロジェクト
-   選択**NuGet パッケージを管理しています.**
-   NuGet パッケージの管理 ダイアログ ボックスで、、**オンライン** タブで選択し、 **EntityFramework**パッケージ
-   クリックして**インストール**  
    > [!NOTE]
    > EntityFramework アセンブリだけでなく System.ComponentModel.DataAnnotations への参照も追加されます。 System.Data.Entity への参照をプロジェクトには場合、は、EntityFramework パッケージがインストールされている場合、削除するされます。 System.Data.Entity アセンブリは Entity Framework 6 アプリケーションは使用されません。

## <a name="implementing-ilistsource-for-collections"></a>コレクションの IListSource の実装

コレクションのプロパティは、双方向データ バインド Windows フォームを使用する場合の並べ替えを有効にする IListSource インターフェイスを実装する必要があります。 IListSource の機能を追加する ObservableCollection を拡張しようとしてこれを行うには。

-   追加、 **ObservableListSource**クラスをプロジェクト。
    -   プロジェクト名を右クリックします。
    -   選択**追加 -&gt;新しい項目**
    -   選択**クラス**入力**ObservableListSource**クラス名
-   既定では、次のコードで生成されたコードに置き換えます。

*このクラスには、双方向データ バインドと並べ替えができるようにします。クラスは ObservableCollection から派生&lt;T&gt; IListSource の明示的な実装を追加します。IListSource の GetList() メソッドは、同期、ObservableCollection を維持する IBindingList 実装を返す実装されます。ToBindingList によって生成された IBindingList 実装では、並べ替えをサポートします。ToBindingList の拡張機能メソッドは、EntityFramework アセンブリで定義されます。*

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

## <a name="define-a-model"></a>モデルを定義します。

できます、このチュートリアルでは、Code First または EF Designer を使用してモデルを実装するために選択しました。 2 つのセクションでは、次のいずれかを完了します。

### <a name="option-1-define-a-model-using-code-first"></a>オプション 1: Code First を使用してモデルを定義します。

このセクションでは、モデルおよび関連する Code First を使用してデータベースを作成する方法を示します。 次のセクションにスキップ (**オプション 2: Database First を使用してモデルを定義)** エンジニア リング EF designer を使用してデータベースからモデルを使用する場合は、データベースの最初を逆にします。

Code First の開発を使用する場合に、(ドメイン) の概念モデルを定義する .NET Framework クラスを記述することで、通常で開始します。

-   新しい追加**製品**プロジェクトにクラス
-   既定では、次のコードで生成されたコードに置き換えます。

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

-   追加、**カテゴリ**クラスをプロジェクト。
-   既定では、次のコードで生成されたコードに置き換えます。

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

エンティティを定義するだけでなくから派生したクラスを定義する必要があります。 **DbContext**公開**DbSet&lt;TEntity&gt;** プロパティ。 **DbSet**プロパティは、モデルに追加する型も把握してコンテキストを使用します。 **DbContext**と**DbSet**型 EntityFramework アセンブリで定義されます。

DbContext 派生型のインスタンスがデータベースからデータを使用してオブジェクトの設定が含まれています。 ランタイム処理中に、エンティティ オブジェクトを管理、追跡、および永続化するデータをデータベースに変更します。

-   新しい追加**ProductContext**クラスをプロジェクト。
-   既定では、次のコードで生成されたコードに置き換えます。

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

    ![ChooseModelContents](~/ef6/media/choosemodelcontents.png)

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

EF では、T4 テンプレートを使用して、モデルからコードを生成します。 Visual Studio に付属のか、Visual Studio ギャラリーからダウンロードしたテンプレートは、一般的な用途に使用されるもの。 つまり、これらのテンプレートから生成されたエンティティがある単純な ICollection&lt;T&gt;プロパティ。 ただし、データ バインディングの実行時は IListSource を実装するコレクションのプロパティを用意することをお勧めします。 これは、ため、上記の ObservableListSource クラスを作成したとするテンプレートを変更しようとして今すぐこのクラスを使用します。

-   開く、**ソリューション エクスプ ローラー**を見つけて**ProductModel.edmx**ファイル
-   検索、 **ProductModel.tt** ProductModel.edmx ファイルの下で入れ子にはファイル

    ![製品モデルのテンプレート](~/ef6/media/productmodeltemplate.png)

-   Visual Studio エディターで開く ProductModel.tt ファイルをダブルクリックします
-   検索し、置換の 2 つの出現回数"**ICollection**"with"**ObservableListSource**"。 これらは、296 と 484 を約行にあります。
-   検索し、置換の最初に見つかった"**HashSet**"with"**ObservableListSource**"。 この状況の発生は、約 50 行に配置されます。 **しない**HashSet は後で、コードの 2 番目に出現を置換します。
-   ProductModel.tt ファイルを保存します。 これは、再生成するエンティティのコードで発生する必要があります。 コードは自動的に再生成しない場合は、ProductModel.tt を右クリックし、「カスタム ツールの実行」を選択します。

かどうか (これは ProductModel.tt 下で入れ子になった) Category.cs ファイルを開くようになりましたし、製品のコレクションが、型を持つことを確認する必要があります**ObservableListSource&lt;製品&gt;** します。

プロジェクトをコンパイルします。

## <a name="lazy-loading"></a>遅延読み込み

**製品**プロパティを**カテゴリ**クラスと**カテゴリ**プロパティを**製品**クラスは、ナビゲーション プロパティ。 Entity Framework では、ナビゲーション プロパティは、2 つのエンティティ型間のリレーションシップを移動する方法を提供します。

EF では、関連エンティティを読み込む、データベースから自動的に初めてナビゲーション プロパティにアクセスするの選ぶことができます。 この種類の読み込み (遅延読み込みと呼ばれます) にするには、初めての各ナビゲーション プロパティにアクセスする別のクエリが実行されること、データベースに対して、内容が既にコンテキストでない場合あります。

POCO エンティティ型を使用する場合、EF は実行時にプロキシの派生型のインスタンスを作成し、読み込み用のフックを追加するクラスの仮想プロパティをオーバーライドして遅延読み込みを実現します。 関連オブジェクトの遅延読み込みを取得する必要がありますを宣言するナビゲーション プロパティの getter として**パブリック**と**仮想**(**Overridable** Visual Basic で)、およびするクラスがすることはできません**シール**(**NotOverridable** Visual Basic で)。 データベースを使用して最初のナビゲーション プロパティは自動的遅延読み込みを有効にするための仮想になります。 コードの最初のセクションで同じ理由から仮想ナビゲーション プロパティを作成することにしました

## <a name="bind-object-to-controls"></a>オブジェクトをコントロールにバインドします。

この WinForms アプリケーションのデータ ソースとしてモデルに定義されているクラスを追加します。

-   メイン メニューで、次のように選択します**プロジェクト -&gt;新しいデータ ソースを追加しています。**
    (Visual Studio 2010 では、選択する必要があります**データ -&gt;新しいデータ ソースの追加...**)
-   データ ソースの種類の選択、次のように選択します**オブジェクト**クリック**次へ。**
-   選択、データ オブジェクト ダイアログ ボックスで、展開、 **WinFormswithEFSample** 2 つの時刻と選択**カテゴリ**製品の使用をいたしますので、製品のデータ ソースを選択する必要はありませんカテゴリのデータ ソースのプロパティです。

    ![データ ソース](~/ef6/media/datasource.png)

-   クリックして**完了します**。
    *データ ソース ウィンドウが表示されていない場合は、選択 * * * ビュー -&gt;その他の Windows-&gt;データ ソース**
-   キーを押して、データ ソース ウィンドウが自動しないように、ピン アイコンを非表示にします。 ウィンドウが表示された場合は、更新ボタンをクリックする必要があります。

    ![データ ソース 2](~/ef6/media/datasource2.png)

-   ソリューション エクスプ ローラーで、 **Form1.cs**メイン フォームをデザイナーで開くファイル。
-   選択、**カテゴリ**データ ソースをフォームにドラッグします。 既定で新しい DataGridView (**categoryDataGridView**) ナビゲーション ツール バー コントロールは、デザイナーに追加されます。 これらのコントロールは BindingSource にバインドされます (**categoryBindingSource**) とバインドのナビゲーター (**categoryBindingNavigator**) も作成されるコンポーネント。
-   列の編集、 **categoryDataGridView**します。 設定する、 **CategoryId**列を読み取り専用にします。 値、 **CategoryId**プロパティは、データを保存した後に、データベースによって生成されます。
    -   DataGridView コントロールを右クリックし、列の編集を選択してください.
    -   CategoryId 列を選択し、ReadOnly を True に設定します。
    -   [Ok]
-   カテゴリのデータ ソースから製品を選択し、フォームにドラッグします。 ProductDataGridView と productBindingSource は、フォームに追加されます。
-   ProductDataGridView で列を編集します。 CategoryId およびカテゴリ列を非表示にして、読み取り専用に ProductId を設定します。 ProductId プロパティの値は、データを保存した後に、データベースによって生成されます。
    -   DataGridView コントロールを右クリックして**列の編集.**.
    -   選択、 **ProductId**およびセット列**ReadOnly**に**True**します。
    -   選択、 **CategoryId**列とキーを押して、**削除**ボタンをクリックします。 これにより、**カテゴリ**列。
    -   **[OK]** を押します。

    これまでに DataGridView コントロールに関連付けられている BindingSource コンポーネントをデザイナー。 次のセクションのコードに追加のコードが categoryBindingSource.DataSource を現在 DbContext で追跡されるエンティティのコレクションに設定します。 ときに製品 categoryBindingSource と productsBindingSource.DataMember プロパティに productsBindingSource.DataSource プロパティを設定した製品、WinForms、カテゴリの下にドラッグ アンド ドロップして、処理します。 このバインドでは、ため、productDataGridView で現在選択されているカテゴリに属する製品だけが表示されます。
-   有効にする、**保存**マウスの右ボタンをクリックして選択ナビゲーション ツールバーのボタン**有効**します。

    ![1 のフォーム デザイナー](~/ef6/media/form1-designer.png)

-   保存のイベント ハンドラーを追加 ボタンをダブルクリックしてボタンをクリックします。 イベント ハンドラーに追加され、フォームの分離コードに移動されます。 コード、 **categoryBindingNavigatorSaveItem\_クリックして**イベント ハンドラーは、次のセクションで追加されます。

## <a name="add-the-code-that-handles-data-interaction"></a>データの操作を処理するコードを追加します。

これで、ProductContext を使用して、データ アクセスを実行するコード追加します。 次に示すように、メイン フォーム ウィンドウのコードを更新します。

コードでは、ProductContext の実行時間の長いインスタンスを宣言します。 ProductContext オブジェクトは、クエリやデータをデータベースに保存に使用されます。 ProductContext インスタンスの Dispose() メソッドは、オーバーライドされた OnClosing メソッドから呼び出されます。 コード コメントでは、コードの動作について詳しく説明します。

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

## <a name="test-the-windows-forms-application"></a>Windows フォーム アプリケーションをテストします。

-   コンパイルと実行して、アプリケーションが機能をテストできます。

    ![1 を形成する前に保存](~/ef6/media/form1beforesave.png)

-   保存した後にストア生成キーを画面に表示しています。

    ![フォームの 1 の後に保存](~/ef6/media/form1aftersave.png)

-   Code First を使用した場合も表示されますが、 **WinFormswithEFSample.ProductContext**データベースが作成されます。

    ![Server オブジェクト エクスプ ローラー](~/ef6/media/serverobjexplorer.png)
