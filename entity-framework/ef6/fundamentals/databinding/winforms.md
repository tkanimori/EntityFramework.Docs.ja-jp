---
title: WinForms を使用したデータバインド-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 80fc5062-2f1c-4dbd-ab6e-b99496784b36
ms.openlocfilehash: 4b3eee20ff238864b94ef4edfb97c1bae0713300
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181787"
---
# <a name="databinding-with-winforms"></a>WinForms を使用したデータバインド
このステップバイステップチュートリアルでは、POCO 型を "マスター/詳細" フォームでウィンドウフォーム (WinForms) コントロールにバインドする方法について説明します。 アプリケーションでは、Entity Framework を使用して、データベースのデータをオブジェクトに読み込み、変更を追跡し、データベースにデータを保持します。

このモデルでは、一対多のリレーションシップに参加する2つの型が定義されています。Category (principal @ no__t-0master) と Product (依存する @ no__t-1detail)。 次に、Visual Studio ツールを使用して、モデルで定義されている型を WinForms コントロールにバインドします。 WinForms データバインディングフレームワークを使用すると、関連するオブジェクト間のナビゲーションが可能になります。マスタービューで行を選択すると、対応する子データで詳細ビューが更新されます。

このチュートリアルのスクリーンショットとコードリストは Visual Studio 2013 から取得されていますが、Visual Studio 2012 または Visual Studio 2010 を使用してこのチュートリアルを完了することができます。

## <a name="pre-requisites"></a>前提条件

このチュートリアルを完了するには、Visual Studio 2013、Visual Studio 2012、または Visual Studio 2010 がインストールされている必要があります。

Visual Studio 2010 を使用している場合は、NuGet もインストールする必要があります。 詳細については、「 [NuGet のインストール](https://docs.nuget.org/docs/start-here/installing-nuget)」を参照してください。

## <a name="create-the-application"></a>アプリケーションを作成する

-   Visual Studio を開く
-   **ファイル-&gt; 新規-@no__t プロジェクト....**
-   左側のウィンドウで **[windows]** を選択し、右側のウィンドウで**windows フォームアプリケーション**を選択します。
-   名前として「 **Winフォーム Withefsample** 」と入力します。
-   **[OK]** を選択します。

## <a name="install-the-entity-framework-nuget-package"></a>Entity Framework NuGet パッケージをインストールする

-   ソリューションエクスプローラーで、 **Winフォーム Withefsample**プロジェクトを右クリックします。
-   **[NuGet パッケージの管理...]** を選択します。
-   NuGet パッケージの管理 ダイアログで、**オンライン** タブを選択し、 **entityframework**パッケージを選択します。
-   **[インストール]** をクリックします。  
    > [!NOTE]
    > EntityFramework アセンブリに加えて、System.componentmodel への参照も追加されます。 プロジェクトに system.object への参照が含まれている場合は、EntityFramework パッケージのインストール時に削除されます。 Entity Framework 6 アプリケーションでは、system.object アセンブリは使用されなくなりました。

## <a name="implementing-ilistsource-for-collections"></a>コレクションの IListSource の実装

コレクションプロパティは、Windows フォームを使用するときに、並べ替えを使用した双方向のデータバインディングを有効にするために、IListSource インターフェイスを実装する必要があります。 これを行うには、System.collections.objectmodel.observablecollection を拡張して IListSource 機能を追加します。

-   **ObservableListSource**クラスをプロジェクトに追加します。
    -   プロジェクト名を右クリックします。
    -   [**追加 &gt; 件の新しい項目**] を選択します。
    -   **[クラス]** を選択し、クラス名として「 **ObservableListSource** 」と入力します。
-   既定で生成されるコードを次のコードに置き換えます。

*This クラスは、双方向のデータバインディングと並べ替えを有効にします。クラスは System.collections.objectmodel.observablecollection @ no__t-0T @ no__t-1 から派生し、IListSource の明示的な実装を追加します。IListSource の GetList () メソッドを実装すると、System.collections.objectmodel.observablecollection との同期を維持した状態で IBindingList 実装が返されます。ToBindingList によって生成される IBindingList 実装は、並べ替えをサポートしています。ToBindingList 拡張メソッドは EntityFramework アセンブリで定義されています。*

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

## <a name="define-a-model"></a>モデルの定義

このチュートリアルでは、Code First または EF デザイナーを使用してモデルを実装することを選択できます。 次の2つのセクションのいずれかを実行します。

### <a name="option-1-define-a-model-using-code-first"></a>オプション 1:Code First を使用してモデルを定義する

このセクションでは、Code First を使用してモデルとそれに関連付けられたデータベースを作成する方法について説明します。 次のセクションに進みます (**Option 2:Database First) を使用してモデルを定義します。 EF デザイナーを使用してデータベースからモデルをリバースエンジニアリングする場合は、Database First を使用する場合は 0 @no__t に設定します。

Code First 開発を使用する場合は、通常、概念 (ドメイン) モデルを定義する .NET Framework クラスを記述することから始めます。

-   新しい**製品**クラスをプロジェクトに追加する
-   既定で生成されるコードを次のコードに置き換えます。

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

-   **カテゴリ**クラスをプロジェクトに追加します。
-   既定で生成されるコードを次のコードに置き換えます。

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

エンティティを定義するだけでなく、 **Dbcontext**から派生し、 **dbcontext @ no__t-2TEntity @ no__t**プロパティを公開するクラスを定義する必要があります。 **Dbset**プロパティを使用すると、モデルに含める型をコンテキストに認識させることができます。 **Dbcontext**および**Dbcontext**型は entityframework アセンブリで定義されています。

DbContext の派生型のインスタンスは、実行時にエンティティオブジェクトを管理します。これには、データベースからのデータを使用したオブジェクトの読み込み、変更の追跡、およびデータベースへのデータの永続化が含まれます。

-   新しい**Productcontext**クラスをプロジェクトに追加します。
-   既定で生成されるコードを次のコードに置き換えます。

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

### <a name="option-2-define-a-model-using-database-first"></a>オプション 2:Database First を使用してモデルを定義する

このセクションでは、Database First を使用して、EF デザイナーを使用してデータベースからモデルをリバースエンジニアリングする方法について説明します。 前のセクション (**Option 1:Code First)**  を使用してモデルを定義し、このセクションをスキップして、**遅延読み込み**のセクションに直接移動します。

#### <a name="create-an-existing-database"></a>既存のデータベースを作成する

通常、既存のデータベースを対象とする場合は、既に作成されますが、このチュートリアルでは、にアクセスするデータベースを作成する必要があります。

Visual Studio と共にインストールされるデータベースサーバーは、インストールされている Visual Studio のバージョンによって異なります。

-   Visual Studio 2010 を使用している場合は、SQL Express データベースを作成します。
-   Visual Studio 2012 を使用している場合は、 [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx)データベースを作成します。

では、データベースを生成してみましょう。

-   **ビュー-&gt; サーバーエクスプローラー**
-   [データ接続] を右クリックし **、&gt; [接続の追加**] をクリックします。
-   サーバーエクスプローラーからデータベースに接続していない場合は、データソースとして Microsoft SQL Server を選択する必要があります。

    ![データ ソースの変更](~/ef6/media/changedatasource.png)

-   インストールされているものに応じて LocalDB または SQL Express に接続し、データベース名として「 **Products** 」と入力します。

    ![接続 LocalDB の追加](~/ef6/media/addconnectionlocaldb.png)

    ![接続 Express の追加](~/ef6/media/addconnectionexpress.png)

-   [ **OK]** を選択すると、新しいデータベースを作成するかどうかを確認するメッセージが表示されます。 **[はい]** を選択します。

    ![データベースの作成](~/ef6/media/createdatabase.png)

-   新しいデータベースがサーバーエクスプローラーに表示され、右クリックして **[新しいクエリ]** を選択します。
-   次の SQL を新しいクエリにコピーし、クエリを右クリックして、 **[実行]** を選択します。

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

-   **プロジェクト-&gt; 新しい項目の追加...**
-   左側のメニューから **データ** を選択し、ADO.NET をクリックし**Entity Data Model**
-   名前として「 **Productmodel** 」と入力し、[ **OK]** をクリックします。
-   **Entity Data Model ウィザード**が起動します。
-   **[データベースから生成]** を選択し、 **[次へ]** をクリックします。

    ![Modelcontents を行う](~/ef6/media/choosemodelcontents.png)

-   最初のセクションで作成したデータベースへの接続を選択し、接続文字列の名前として「 **Productcontext** 」と入力して、 **[次へ]** をクリックします。

    ![接続の選択](~/ef6/media/chooseyourconnection.png)

-   [テーブル] の横にあるチェックボックスをオンにしてすべてのテーブルをインポートし、[完了] をクリックします。

    ![オブジェクトの選択](~/ef6/media/chooseyourobjects.png)

リバースエンジニアリングプロセスが完了すると、新しいモデルがプロジェクトに追加され、Entity Framework Designer で表示できるように開かれます。 App.config ファイルも、データベースの接続の詳細と共にプロジェクトに追加されています。

#### <a name="additional-steps-in-visual-studio-2010"></a>Visual Studio 2010 の追加手順

Visual Studio 2010 で作業している場合は、EF6 コード生成を使用するように EF デザイナーを更新する必要があります。

-   EF デザイナーでモデルの空の場所を右クリックし、 **[コード生成項目の追加...]** を選択します。
-   左側のメニューから **[オンラインテンプレート]** を選択し、 **dbcontext**を検索します。
-   **C @ no__t の EF 6.X DbContext ジェネレーター**を選択し、名前として「製品**モデル**」と入力して、[追加] をクリックします。

#### <a name="updating-code-generation-for-data-binding"></a>データバインディングのコード生成の更新

EF は T4 テンプレートを使用して、モデルからコードを生成します。 Visual Studio に付属しているテンプレート、または Visual Studio ギャラリーからダウンロードしたテンプレートは、汎用的な使用を目的としています。 これは、これらのテンプレートから生成されたエンティティに、単純な ICollection @ no__t-0T @ no__t プロパティがあることを意味します。 ただし、データバインディングを実行する場合は、IListSource を実装するコレクションプロパティを設定することをお勧めします。 このため、上記の ObservableListSource クラスを作成したので、このクラスを使用するようにテンプレートを変更します。

-   **ソリューションエクスプローラー**を開き、 **productmodel. .edmx**ファイルを検索します。
-   ProductModel. .edmx ファイルの下に入れ子になっている**ProductModel.tt**ファイルを見つけます。

    ![製品モデルテンプレート](~/ef6/media/productmodeltemplate.png)

-   ProductModel.tt ファイルをダブルクリックして、Visual Studio エディターで開きます。
-   "**ICollection**" の2つの出現箇所を検索し、"**ObservableListSource**" に置き換えます。 これらは、約296と484にあります。
-   最初に出現する "**HashSet**" を検索し、"**ObservableListSource**" に置き換えます。 この現象は、約50行目にあります。 コードの後半で見つかった HashSet の2回目の出現を置き換え**ないでください**。
-   ProductModel.tt ファイルを保存します。 これにより、エンティティのコードが再生成されます。 コードが自動的に再生成されない場合は、ProductModel.tt を右クリックし、[カスタムツールの実行] を選択します。

Category.cs ファイル (ProductModel.tt の下に入れ子になっています) を開くと、Products コレクションの種類が**ObservableListSource @ no__t-1Product @ no__t-2**になっていることがわかります。

プロジェクトをコンパイルします。

## <a name="lazy-loading"></a>遅延読み込み

**Product**クラスの**Category**クラスおよび**category**プロパティの**Products**プロパティは、ナビゲーションプロパティです。 Entity Framework では、ナビゲーションプロパティを使用して、2つのエンティティ型間のリレーションシップをナビゲートすることができます。

EF では、ナビゲーションプロパティに初めてアクセスするときに、関連エンティティをデータベースから自動的に読み込むことができます。 この種類の読み込み (遅延読み込みと呼ばれます) を使用すると、各ナビゲーションプロパティに初めてアクセスしたときに、コンテンツがコンテキスト内に存在しない場合、データベースに対して個別のクエリが実行されることに注意してください。

POCO エンティティ型を使用する場合、EF は、実行時に派生プロキシ型のインスタンスを作成し、クラスの仮想プロパティをオーバーライドして読み込みフックを追加することにより、遅延読み込みを実現します。 関連オブジェクトの遅延読み込みを行うには、ナビゲーションプロパティ getter を**public**および**virtual** (Visual Basic で**オーバーライド**可能) として宣言し、クラスを**sealed** (Visual Basic では**NotOverridable** ) にする必要があります。 Database First を使用すると、遅延読み込みを有効にするために、ナビゲーションプロパティが自動的に仮想化されます。 Code First セクションでは、ナビゲーションプロパティを同じ理由で仮想にすることを選択しました。

## <a name="bind-object-to-controls"></a>コントロールへのオブジェクトのバインド

この WinForms アプリケーションのデータソースとして、モデルで定義されているクラスを追加します。

-   メイン メニューで、次のように選択します**プロジェクト -&gt;新しいデータ ソースを追加しています...**
    (Visual Studio 2010 では、[**データ-&gt; 新しいデータソースの追加**] を選択する必要があります)。
-   データソースの種類を選択 ウィンドウで、**オブジェクト** を選択し、**次へ** をクリックします。
-   [データオブジェクトの選択] ダイアログで、 **Winフォーム Withefsample**を2回展開し、 **[カテゴリ]** を選択します。製品データソースを選択する必要はありません。これは、カテゴリデータソースで製品のプロパティを使用して取得するからです。

    ![[データ ソース]](~/ef6/media/datasource.png)

-   [**完了] をクリックします。**
    [データソース] ウィンドウが表示されていない場合は、[**表示-&gt; つの他の Windows-@no__t データソース**] を選択します。
-   [データソース] ウィンドウが自動的に非表示にならないように、ピンアイコンを押します。 ウィンドウが既に表示されている場合は、[更新] ボタンをクリックしなければならないことがあります。

    ![データソース2](~/ef6/media/datasource2.png)

-   ソリューションエクスプローラーで、 **Form1.cs**ファイルをダブルクリックして、デザイナーでメインフォームを開きます。
-   **カテゴリ**データソースを選択し、フォーム上にドラッグします。 既定では、新しい DataGridView (**カテゴリ DataGridView**) コントロールとナビゲーションツールバーコントロールがデザイナーに追加されます。 これらのコントロールは、作成された BindingSource (**カテゴリ bindingsource**) コンポーネントとバインドナビゲーター (**カテゴリ bindingnavigator**) コンポーネントにバインドされます。
-   **カテゴリ datagridview**の列を編集します。 **CategoryId**列を読み取り専用に設定します。 **CategoryId**プロパティの値は、データを保存した後にデータベースによって生成されます。
    -   DataGridView コントロールを右クリックし、[列の編集...] を選択します。
    -   CategoryId 列を選択し、ReadOnly を True に設定します。
    -   [OK] を押します。
-   カテゴリデータソースの下にある [Products] を選択し、フォームにドラッグします。 ProductDataGridView と Productdatagridview がフォームに追加されます。
-   ProductDataGridView の列を編集します。 CategoryId 列と Category 列を非表示にし、ProductId を読み取り専用に設定します。 ProductId プロパティの値は、データを保存した後にデータベースによって生成されます。
    -   DataGridView コントロールを右クリックし、 **[列の編集...]** を選択します。
    -   **ProductId**列を選択し、 **ReadOnly**を**True**に設定します。
    -   **CategoryId**列を選択し、 **[削除]** をクリックします。 **Category**列でも同じ操作を行います。
    -   **[OK]** を押します。

    ここまでで、DataGridView コントロールはデザイナーの BindingSource コンポーネントに関連付けられていました。 次のセクションでは、コードを分離コードに追加して、現在 DbContext によって追跡されているエンティティのコレクションにカテゴリ Bindingsource を設定します。 カテゴリの下から製品をドラッグアンドドロップした場合、WinForms プロパティを Category Bindingsource に設定し、Products に製品を設定することが処理されました。 このバインドにより、現在選択されているカテゴリに属する製品だけが productDataGridView に表示されます。
-   ナビゲーションツールバーの **[保存]** ボタンを有効にするには、マウスの右ボタンをクリックし、 **[有効]** を選択します。

    ![フォーム1デザイナー](~/ef6/media/form1-designer.png)

-   [保存] ボタンのイベントハンドラーを、ボタンをダブルクリックして追加します。 これにより、イベントハンドラーが追加され、フォームの分離コードに移動します。 **CategoryBindingNavigatorSaveItem @ no__t-1click**イベントハンドラーのコードは、次のセクションで追加されます。

## <a name="add-the-code-that-handles-data-interaction"></a>データの相互作用を処理するコードを追加する

次に、ProductContext を使用してデータアクセスを実行するコードを追加します。 次に示すように、メインフォームウィンドウのコードを更新します。

このコードは、ProductContext の実行時間の長いインスタンスを宣言します。 ProductContext オブジェクトは、データをクエリしてデータベースに保存するために使用されます。 その後、ProductContext インスタンスの Dispose () メソッドが、オーバーライドされた OnClosing メソッドから呼び出されます。 コードのコメントには、コードの動作についての詳細が記載されています。

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

## <a name="test-the-windows-forms-application"></a>Windows フォームアプリケーションのテスト

-   アプリケーションをコンパイルして実行すると、機能をテストできます。

    ![保存前のフォーム1](~/ef6/media/form1beforesave.png)

-   保存すると、ストアによって生成されたキーが画面に表示されます。

    ![保存後のフォーム1](~/ef6/media/form1aftersave.png)

-   Code First を使用した場合は、 **Winフォーム Withefsample. ProductContext**データベースが作成されていることもわかります。

    ![サーバーオブジェクトエクスプローラー](~/ef6/media/serverobjexplorer.png)
