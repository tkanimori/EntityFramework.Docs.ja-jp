---
title: 自己追跡エンティティのチュートリアル - EF6
author: divega
ms.date: 2016-10-23
ms.assetid: b21207c9-1d95-4aa3-ae05-bc5fe300dab0
ms.openlocfilehash: 1c450bbb20c246d9b9d58707ac03eb48eadfa970
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2018
ms.locfileid: "44251285"
---
# <a name="self-tracking-entities-walkthrough"></a>自己追跡エンティティのチュートリアル
> [!IMPORTANT]
> 自己追跡エンティティ テンプレートの使用は現在お勧めしていません。 既存のアプリケーションをサポートするためにのみ引き続き使用できます。 アプリケーションで、エンティティの切断されたグラフを操作する必要がある場合は、代替の方法を検討してください。たとえば、コミュニティによってより積極的に開発された自己追跡エンティティに似たテクノロジである[追跡可能なエンティティ](http://trackableentities.github.io/)を使用するか、または、低レベルの変更追跡 API を使用してカスタム コードを記述してください。

このチュートリアルでは、Windows Communication Foundation (WCF) サービスをエンティティ グラフを返す操作を公開するシナリオを示します。 次に、クライアント アプリケーションでは、そのグラフを操作し、検証および Entity Framework を使用してデータベースに、更新プログラムを保存するサービス操作への変更を送信します。

このチュートリアルを完了する前に確認してください、[自己追跡エンティティ](index.md)ページ。

このチュートリアルでは次の操作を行います。

-   アクセスするデータベースを作成します。
-   モデルを含むクラス ライブラリを作成します。
-   Self-Tracking Entity Generator テンプレートをスワップします。
-   エンティティ クラスを別のプロジェクトに移動します。
-   クエリを実行し、エンティティを保存する操作を公開する WCF サービスを作成します。
-   サービスを使用するアプリケーション (コンソールおよび WPF) クライアントを作成します。

このチュートリアルでは Database First 使用しますが、同じ手法は、モデルを最初に等しく適用されます。

## <a name="pre-requisites"></a>前提条件

このチュートリアルを完了するには、Visual Studio の最新バージョンを必要があります。

## <a name="create-a-database"></a>データベースを作成します。

Visual Studio と共にインストールされているデータベース サーバーは、インストールした Visual Studio のバージョンによって異なります。

-   Visual Studio 2012 を使用している場合、作成する LocalDB データベース。
-   Visual Studio 2010 を使用している場合は、SQL Express データベースを作成するがします。

データベースを生成してみましょう。

-   Visual Studio を開く
-   **ビュー -&gt;サーバー エクスプ ローラー**
-   右クリックして**データ接続 -&gt;接続の追加.**
-   まだデータベースに接続して、サーバー エクスプ ローラーから選択する必要があります前に場合**Microsoft SQL Server**データ ソースとして
-   LocalDB または SQL Express をインストールしたものによってのいずれかに接続します。
-   入力**STESample**データベース名として
-   選択**OK**かどうかは、新しいデータベースを作成するように要求がある **[はい]**
-   新しいデータベースがサーバー エクスプ ローラーに表示されます。
-   Visual Studio 2012 を使用している場合
    -   サーバー エクスプ ローラーでデータベースを右クリックし、選択**新しいクエリ**
    -   新しいクエリに、次の SQL をコピーし、クエリを選択します右クリックし、 **Execute**
-   Visual Studio 2010 を使用している場合
    -   選択**データ -&gt; Transact SQL エディター -&gt;新しいクエリ接続しています.**
    -   入力 **.\\SQLEXPRESS**サーバー名をクリックします**OK**
    -   選択、 **STESample**クエリ エディターの上部にある下のドロップダウンからデータベース
    -   新しいクエリに、次の SQL をコピーし、クエリを選択します右クリックし、 **SQL の実行**

``` SQL
    CREATE TABLE [dbo].[Blogs] (
        [BlogId] INT IDENTITY (1, 1) NOT NULL,
        [Name] NVARCHAR (200) NULL,
        [Url]  NVARCHAR (200) NULL,
        CONSTRAINT [PK_dbo.Blogs] PRIMARY KEY CLUSTERED ([BlogId] ASC)
    );

    CREATE TABLE [dbo].[Posts] (
        [PostId] INT IDENTITY (1, 1) NOT NULL,
        [Title] NVARCHAR (200) NULL,
        [Content] NTEXT NULL,
        [BlogId] INT NOT NULL,
        CONSTRAINT [PK_dbo.Posts] PRIMARY KEY CLUSTERED ([PostId] ASC),
        CONSTRAINT [FK_dbo.Posts_dbo.Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [dbo].[Blogs] ([BlogId]) ON DELETE CASCADE
    );

    SET IDENTITY_INSERT [dbo].[Blogs] ON
    INSERT INTO [dbo].[Blogs] ([BlogId], [Name], [Url]) VALUES (1, N'ADO.NET Blog', N'blogs.msdn.com/adonet')
    SET IDENTITY_INSERT [dbo].[Blogs] OFF
    INSERT INTO [dbo].[Posts] ([Title], [Content], [BlogId]) VALUES (N'Intro to EF', N'Interesting stuff...', 1)
    INSERT INTO [dbo].[Posts] ([Title], [Content], [BlogId]) VALUES (N'What is New', N'More interesting stuff...', 1)
```

## <a name="create-the-model"></a>モデルを作成します。

Up、まず最初に、モデルを配置するプロジェクト。

-   **ファイル -&gt;新機能 -&gt;プロジェクト.**
-   選択**Visual C\#** 左側のウィンドウからし**クラス ライブラリ**
-   入力**STESample**名をクリックします**OK**

データベースへのアクセスに EF Designer で単純なモデルを作成します。

-   **プロジェクト -&gt;新しい項目を追加しています.**
-   選択**データ**左側のウィンドウからし**ADO.NET Entity Data Model**
-   入力**BloggingModel**名をクリックします**OK**
-   選択**データベースから生成**クリック **[次へ]**
-   前のセクションで作成したデータベースの接続情報を入力します。
-   入力**BloggingContext**し接続文字列の名前として **[次へ]**
-   次に、ボックスをオン**テーブル**クリック **[完了]**

## <a name="swap-to-ste-code-generation"></a>コード生成の STE に切り替えます

既定のコード生成とスワップ自己追跡エンティティを無効にする必要があります。

### <a name="if-you-are-using-visual-studio-2012"></a>Visual Studio 2012 を使用している場合

-   展開**BloggingModel.edmx**で**ソリューション エクスプ ローラー**を削除し、 **BloggingModel.tt**と**BloggingModel.Context.tt** 
    *これが既定のコード生成を無効になります*
-   EF デザイナー サーフェスと選択の空の領域を右クリックして**コード生成項目の追加.**
-   選択**オンライン**、左側のウィンドウの検索から**STE ジェネレーター**
-   選択、 **C の STE ジェネレーター\#** テンプレート入力**STETemplate**名をクリックします**追加**
-   **STETemplate.tt**と**STETemplate.Context.tt** BloggingModel.edmx ファイルの下で入れ子になったファイルが追加されます

### <a name="if-you-are-using-visual-studio-2010"></a>Visual Studio 2010 を使用している場合

-   EF デザイナー サーフェスと選択の空の領域を右クリックして**コード生成項目の追加.**
-   選択**コード**左側のウィンドウからし**ADO.NET Self-Tracking Entity Generator**
-   入力**STETemplate**名をクリックします**追加**
-   **STETemplate.tt**と**STETemplate.Context.tt**ファイルがプロジェクトに直接追加されます

## <a name="move-entity-types-into-separate-project"></a>エンティティ型を別のプロジェクトに移動します。

自己追跡エンティティを使用して、クライアント アプリケーションには、モデルから生成されたエンティティ クラスへのアクセスが必要があります。 クライアント アプリケーションにモデル全体を公開するたくないので、エンティティ クラスを別のプロジェクトに移動いきます。

最初の手順では、既存のプロジェクトでエンティティ クラスの生成を停止します。

-   右クリックして**STETemplate.tt**で**ソリューション エクスプ ローラー**選択**プロパティ**
-   **プロパティ**ウィンドウのクリア**TextTemplatingFileGenerator**から、 **CustomTool**プロパティ
-   展開**STETemplate.tt**で**ソリューション エクスプ ローラー**およびその下にある入れ子になったすべてのファイルの削除

次に、新しいプロジェクトを追加し、エンティティ クラスを生成するつもりは

-   **ファイル -&gt;追加 -&gt;プロジェクト.**
-   選択**Visual C\#** 左側のウィンドウからし**クラス ライブラリ**
-   入力**STESample.Entities**名をクリックします**OK**
-   **プロジェクト -&gt;既存の項目を追加しています.**
-   移動し、 **STESample**プロジェクト フォルダー
-   表示する選択**すべてのファイル (\*.\*)**
-   選択、 **STETemplate.tt**ファイル
-   次に、ドロップダウン矢印をクリックして、**追加**ボタンをクリックし、選択**リンクとして追加**

    ![リンクされたテンプレートを追加します。](~/ef6/media/addlinkedtemplate.png)

今回は、コンテキストと同じ名前空間で生成されたエンティティ クラスになっていることを確認します。 これだけのステートメントは、アプリケーション全体で追加する必要がありますを使用して数を減らします。

-   リンクを右クリックして**STETemplate.tt**で**ソリューション エクスプ ローラー**選択**プロパティ**
-   **プロパティ**ウィンドウ セット**カスタム ツールの Namespace**に**STESample**

STE テンプレートによって生成されたコードへの参照は必要があります**System.Runtime.Serialization**コンパイルするためにします。 このライブラリは、WCF に必要な**DataContract**と**DataMember**シリアル化可能なエンティティ型で使用される属性。

-   右クリックして、 **STESample.Entities**プロジェクト**ソリューション エクスプ ローラー**選択**参照の追加.**
    -   Visual Studio 2012 - チェック ボックスの横に**System.Runtime.Serialization**クリック**OK**
    -   Visual Studio 2010 では、次のように選択します**System.Runtime.Serialization**クリック **[ok]。**

最後に、これで、コンテキストを使用してプロジェクトには、エンティティ型への参照を必要があります。

-   右クリックして、 **STESample**プロジェクト**ソリューション エクスプ ローラー**選択**参照の追加.**
    -   Visual Studio 2012 - 選択**ソリューション**横にチェック ボックスをオン、左側のウィンドウから**STESample.Entities**  をクリック**ok**
    -   Visual Studio 2010 での選択、**プロジェクト** タブで  **STESample.Entities**  をクリック**OK**

>[!NOTE]
> エンティティ型を別のプロジェクトに移動するためのもう 1 つのオプションでは、既定の場所からリンクすることではなく、テンプレート ファイルを移動します。 これを行うと、更新する必要があります、 **inputFile** edmx ファイルへの相対パスを提供するテンプレートの変数 (はこの例では **..\\BloggingModel.edmx**)。

## <a name="create-a-wcf-service"></a>WCF サービスを作成します。

データを公開する WCF サービスを追加するときは、これで、プロジェクトの作成から始めます。

-   **ファイル -&gt;追加 -&gt;プロジェクト.**
-   選択**Visual C\#** 左側のウィンドウからし**WCF サービス アプリケーション**
-   入力**STESample.Service**名をクリックします**OK**
-   参照を追加、 **System.Data.Entity**アセンブリ
-   参照を追加、 **STESample**と**STESample.Entities**プロジェクト

実行時に見つかったように、このプロジェクトに EF 接続文字列をコピーする必要があります。

-   開く、 **App.Config**ファイルを * * STESample * * プロジェクトとコピー、 **connectionStrings**要素
-   貼り付け、 **connectionStrings**要素の子要素として、**構成**の要素、 **Web.Config**ファイル、 **STESample.Service**プロジェクト

ここには、実際のサービスを実装します。

-   開いている**IService1.cs**内容を次のコードに置き換えます

``` csharp
    using System.Collections.Generic;
    using System.ServiceModel;

    namespace STESample.Service
    {
        [ServiceContract]
        public interface IService1
        {
            [OperationContract]
            List<Blog> GetBlogs();

            [OperationContract]
            void UpdateBlog(Blog blog);
        }
    }
```

-   開いている**Service1.svc**内容を次のコードに置き換えます

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data;
    using System.Linq;

    namespace STESample.Service
    {
        public class Service1 : IService1
        {
            /// <summary>
            /// Gets all the Blogs and related Posts.
            /// </summary>
            public List<Blog> GetBlogs()
            {
                using (BloggingContext context = new BloggingContext())
                {
                    return context.Blogs.Include("Posts").ToList();
                }
            }

            /// <summary>
            /// Updates Blog and its related Posts.
            /// </summary>
            public void UpdateBlog(Blog blog)
            {
                using (BloggingContext context = new BloggingContext())
                {
                    try
                    {
                        // TODO: Perform validation on the updated order before applying the changes.

                        // The ApplyChanges method examines the change tracking information
                        // contained in the graph of self-tracking entities to infer the set of operations
                        // that need to be performed to reflect the changes in the database.
                        context.Blogs.ApplyChanges(blog);
                        context.SaveChanges();

                    }
                    catch (UpdateException)
                    {
                        // To avoid propagating exception messages that contain sensitive data to the client tier
                        // calls to ApplyChanges and SaveChanges should be wrapped in exception handling code.
                        throw new InvalidOperationException("Failed to update. Try your request again.");
                    }
                }
            }        
        }
    }
```

## <a name="consume-the-service-from-a-console-application"></a>コンソール アプリケーションからサービスを使用します。

サービスを使用するコンソール アプリケーションを作成しましょう。

-   **ファイル -&gt;新機能 -&gt;プロジェクト.**
-   選択**Visual C\#** 左側のウィンドウからし**コンソール アプリケーション**
-   入力**STESample.ConsoleTest**名をクリックします**OK**
-   参照を追加、 **STESample.Entities**プロジェクト

WCF サービスにサービス参照を必要があります。

-   右クリックし、 **STESample.ConsoleTest**プロジェクト**ソリューション エクスプ ローラー**選択**サービス参照の追加.**
-   クリックして**検出**
-   入力**BloggingService**名前空間をクリックします**OK**

サービスを使用するいくつかのコードを書きます。

-   開いている**Program.cs**内容を次のコードに置き換えます。

``` csharp
    using STESample.ConsoleTest.BloggingService;
    using System;
    using System.Linq;

    namespace STESample.ConsoleTest
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Print out the data before we change anything
                Console.WriteLine("Initial Data:");
                DisplayBlogsAndPosts();

                // Add a new Blog and some Posts
                AddBlogAndPost();
                Console.WriteLine("After Adding:");
                DisplayBlogsAndPosts();

                // Modify the Blog and one of its Posts
                UpdateBlogAndPost();
                Console.WriteLine("After Update:");
                DisplayBlogsAndPosts();

                // Delete the Blog and its Posts
                DeleteBlogAndPost();
                Console.WriteLine("After Delete:");
                DisplayBlogsAndPosts();

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            static void DisplayBlogsAndPosts()
            {
                using (var service = new Service1Client())
                {
                    // Get all Blogs (and Posts) from the service
                    // and print them to the console
                    var blogs = service.GetBlogs();
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(blog.Name);
                        foreach (var post in blog.Posts)
                        {
                            Console.WriteLine(" - {0}", post.Title);
                        }
                    }
                }

                Console.WriteLine();
                Console.WriteLine();
            }

            static void AddBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Create a new Blog with a couple of Posts
                    var newBlog = new Blog
                    {
                        Name = "The New Blog",
                        Posts =
                        {
                            new Post { Title = "Welcome to the new blog"},
                            new Post { Title = "What's new on the new blog"}
                        }
                    };

                    // Save the changes using the service
                    service.UpdateBlog(newBlog);
                }
            }

            static void UpdateBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Get all the Blogs
                    var blogs = service.GetBlogs();

                    // Use LINQ to Objects to find The New Blog
                    var blog = blogs.First(b => b.Name == "The New Blog");

                    // Update the Blogs name
                    blog.Name = "The Not-So-New Blog";

                    // Update one of the related posts
                    blog.Posts.First().Content = "Some interesting content...";

                    // Save the changes using the service
                    service.UpdateBlog(blog);
                }
            }

            static void DeleteBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Get all the Blogs
                    var blogs = service.GetBlogs();

                    // Use LINQ to Objects to find The Not-So-New Blog
                    var blog = blogs.First(b => b.Name == "The Not-So-New Blog");

                    // Mark all related Posts for deletion
                    // We need to call ToList because each Post will be removed from the
                    // Posts collection when we call MarkAsDeleted
                    foreach (var post in blog.Posts.ToList())
                    {
                        post.MarkAsDeleted();
                    }

                    // Mark the Blog for deletion
                    blog.MarkAsDeleted();

                    // Save the changes using the service
                    service.UpdateBlog(blog);
                }
            }
        }
    }
```

アプリケーションを実行して動作を確認できるようになりました。

-   右クリックし、 **STESample.ConsoleTest**プロジェクト**ソリューション エクスプ ローラー**選択**デバッグ -&gt;新しいインスタンスを開始**

アプリケーションを実行するときは、次の出力を確認します。

```
Initial Data:
ADO.NET Blog
- Intro to EF
- What is New

After Adding:
ADO.NET Blog
- Intro to EF
- What is New
The New Blog
- Welcome to the new blog
- What's new on the new blog

After Update:
ADO.NET Blog
- Intro to EF
- What is New
The Not-So-New Blog
- Welcome to the new blog
- What's new on the new blog

After Delete:
ADO.NET Blog
- Intro to EF
- What is New

Press any key to exit...
```

## <a name="consume-the-service-from-a-wpf-application"></a>WPF アプリケーションからサービスを使用します。

サービスを使用する WPF アプリケーションを作成しましょう。

-   **ファイル -&gt;新機能 -&gt;プロジェクト.**
-   選択**Visual C\#** 左側のウィンドウからし**WPF アプリケーション**
-   入力**STESample.WPFTest**名をクリックします**OK**
-   参照を追加、 **STESample.Entities**プロジェクト

WCF サービスにサービス参照を必要があります。

-   右クリックし、 **STESample.WPFTest**プロジェクト**ソリューション エクスプ ローラー**選択**サービス参照の追加.**
-   クリックして**検出**
-   入力**BloggingService**名前空間をクリックします**OK**

サービスを使用するいくつかのコードを書きます。

-   開いている**MainWindow.xaml**内容を次のコードに置き換えます。

``` xaml
    <Window
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:STESample="clr-namespace:STESample;assembly=STESample.Entities"
        mc:Ignorable="d" x:Class="STESample.WPFTest.MainWindow"
        Title="MainWindow" Height="350" Width="525" Loaded="Window_Loaded">

        <Window.Resources>
            <CollectionViewSource
                x:Key="blogViewSource"
                d:DesignSource="{d:DesignInstance {x:Type STESample:Blog}, CreateList=True}"/>
            <CollectionViewSource
                x:Key="blogPostsViewSource"
                Source="{Binding Posts, Source={StaticResource blogViewSource}}"/>
        </Window.Resources>

        <Grid DataContext="{StaticResource blogViewSource}">
            <DataGrid AutoGenerateColumns="False" EnableRowVirtualization="True"
                      ItemsSource="{Binding}" Margin="10,10,10,179">
                <DataGrid.Columns>
                    <DataGridTextColumn Binding="{Binding BlogId}" Header="Id" Width="Auto" IsReadOnly="True" />
                    <DataGridTextColumn Binding="{Binding Name}" Header="Name" Width="Auto"/>
                    <DataGridTextColumn Binding="{Binding Url}" Header="Url" Width="Auto"/>
                </DataGrid.Columns>
            </DataGrid>
            <DataGrid AutoGenerateColumns="False" EnableRowVirtualization="True"
                      ItemsSource="{Binding Source={StaticResource blogPostsViewSource}}" Margin="10,145,10,38">
                <DataGrid.Columns>
                    <DataGridTextColumn Binding="{Binding PostId}" Header="Id" Width="Auto"  IsReadOnly="True"/>
                    <DataGridTextColumn Binding="{Binding Title}" Header="Title" Width="Auto"/>
                    <DataGridTextColumn Binding="{Binding Content}" Header="Content" Width="Auto"/>
                </DataGrid.Columns>
            </DataGrid>
            <Button Width="68" Height="23" HorizontalAlignment="Right" VerticalAlignment="Bottom"
                    Margin="0,0,10,10" Click="buttonSave_Click">Save</Button>
        </Grid>
    </Window>
```

-   MainWindow の分離コードを開きます (**MainWindow.xaml.cs**)、内容を次のコードに置き換えます

``` csharp
    using STESample.WPFTest.BloggingService;
    using System.Collections.Generic;
    using System.Linq;
    using System.Windows;
    using System.Windows.Data;

    namespace STESample.WPFTest
    {
        public partial class MainWindow : Window
        {
            public MainWindow()
            {
                InitializeComponent();
            }

            private void Window_Loaded(object sender, RoutedEventArgs e)
            {
                using (var service = new Service1Client())
                {
                    // Find the view source for Blogs and populate it with all Blogs (and related Posts)
                    // from the Service. The default editing functionality of WPF will allow the objects
                    // to be manipulated on the screen.
                    var blogsViewSource = (CollectionViewSource)this.FindResource("blogViewSource");
                    blogsViewSource.Source = service.GetBlogs().ToList();
                }
            }

            private void buttonSave_Click(object sender, RoutedEventArgs e)
            {
                using (var service = new Service1Client())
                {
                    // Get the blogs that are bound to the screen
                    var blogsViewSource = (CollectionViewSource)this.FindResource("blogViewSource");
                    var blogs = (List<Blog>)blogsViewSource.Source;

                    // Save all Blogs and related Posts
                    foreach (var blog in blogs)
                    {
                        service.UpdateBlog(blog);
                    }

                    // Re-query for data to get database-generated keys etc.
                    blogsViewSource.Source = service.GetBlogs().ToList();
                }
            }
        }
    }
```

アプリケーションを実行して動作を確認できるようになりました。

-   右クリックし、 **STESample.WPFTest**プロジェクト**ソリューション エクスプ ローラー**選択**デバッグ -&gt;新しいインスタンスを開始**
-   画面を使用してデータを操作してを介してサービスを使用して、保存、**保存**ボタン

![WPF のメイン ウィンドウ](~/ef6/media/wpf.png)
