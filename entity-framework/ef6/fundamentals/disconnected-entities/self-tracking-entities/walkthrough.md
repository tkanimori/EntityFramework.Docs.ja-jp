---
title: 自己追跡エンティティのチュートリアル-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: b21207c9-1d95-4aa3-ae05-bc5fe300dab0
ms.openlocfilehash: 9bd644461f50a7eff1006cb8866ca9a3b08b6b8d
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416145"
---
# <a name="self-tracking-entities-walkthrough"></a>自己追跡エンティティのチュートリアル
> [!IMPORTANT]
> 自己追跡エンティティ テンプレートの使用は現在お勧めしていません。 既存のアプリケーションをサポートするためにのみ引き続き使用できます。 アプリケーションで、エンティティの切断されたグラフを操作する必要がある場合は、代替の方法を検討してください。たとえば、コミュニティによってより積極的に開発された自己追跡エンティティに似たテクノロジである[追跡可能なエンティティ](https://trackableentities.github.io/)を使用するか、または、低レベルの変更追跡 API を使用してカスタム コードを記述してください。

このチュートリアルでは、Windows Communication Foundation (WCF) サービスが、エンティティグラフを返す操作を公開するシナリオについて説明します。 次に、クライアントアプリケーションは、そのグラフを操作し、Entity Framework を使用して更新を検証してデータベースに保存するサービス操作に変更を送信します。

このチュートリアルを完了する前に、「[自己追跡エンティティ](index.md)」ページを必ずお読みください。

このチュートリアルでは次の操作を行います。

-   アクセスするデータベースを作成します。
-   モデルを含むクラスライブラリを作成します。
-   自己追跡エンティティジェネレーターテンプレートにスワップします。
-   エンティティクラスを別のプロジェクトに移動します。
-   エンティティを照会および保存する操作を公開する WCF サービスを作成します。
-   サービスを使用するクライアントアプリケーション (コンソールと WPF) を作成します。

このチュートリアルでは Database First を使用しますが、同じ手法が Model First にも同様に適用されます。

## <a name="pre-requisites"></a>前提条件

このチュートリアルを完了するには、Visual Studio の最新バージョンが必要です。

## <a name="create-a-database"></a>データベースの作成

Visual Studio と共にインストールされるデータベースサーバーは、インストールされている Visual Studio のバージョンによって異なります。

-   Visual Studio 2012 を使用している場合は、LocalDB データベースを作成します。
-   Visual Studio 2010 を使用している場合は、SQL Express データベースを作成します。

では、データベースを生成してみましょう。

-   Visual Studio を開きます
-   **ビュー&gt; サーバーエクスプローラー**
-   **[データ接続]** を右クリックし&gt; [接続の追加] をクリックします。
-   サーバーエクスプローラーからデータベースに接続していない場合は、データソースとして**Microsoft SQL Server**を選択する必要があります。
-   インストールされているものに応じて、LocalDB または SQL Express に接続します。
-   データベース名として「 **STESample** 」と入力します。
-   [ **OK]** を選択すると、新しいデータベースを作成するかどうかを確認するメッセージが表示されます。 **[はい]** を選択します。
-   新しいデータベースがに表示されるようになりサーバーエクスプローラー
-   Visual Studio 2012 を使用している場合
    -   サーバーエクスプローラーでデータベースを右クリックし、 **[新しいクエリ]** を選択します。
    -   次の SQL を新しいクエリにコピーし、クエリを右クリックして、 **[実行]** を選択します。
-   Visual Studio 2010 を使用している場合
    -   [データ] を選択し **&gt; TRANSACT SQL エディター-&gt; 新しいクエリ接続...**
    -   「」と入力し、サーバー名として\\SQLEXPRESS を入力し、[ **OK]** をクリックし**ます。**
    -   クエリエディターの上部にあるドロップダウンから**STESample**データベースを選択します。
    -   次の SQL を新しいクエリにコピーし、クエリを右クリックして、 **[sql の実行]** を選択します。

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

## <a name="create-the-model"></a>モデルを作成する

まず、モデルを配置するためのプロジェクトが必要です。

-   **ファイル&gt; 新規&gt; プロジェクト...**
-   左側のウィンドウで [ **Visual C\#** ]、 **[クラスライブラリ]** の順に選択します。
-   名前として「 **STESample** 」と入力し、[ **OK]** をクリックします。

次に、EF デザイナーで単純なモデルを作成して、データベースにアクセスします。

-   **プロジェクト-新しい項目の追加&gt;...**
-   左側のウィンドウから **[データ]** を選択し、次に**ADO.NET Entity Data Model**
-   名前として「 **Bのログインモデル**」と入力し、[ **OK]** をクリックします。
-   **[データベースから生成]** を選択し、 **[次へ]** をクリックします。
-   前のセクションで作成したデータベースの接続情報を入力します。
-   接続文字列の名前として「 **Bのログインコンテキスト**」と入力し、 **[次へ]** をクリックします。
-   **[テーブル]** の横にあるチェックボックスをオンにし、 **[完了]** をクリックします。

## <a name="swap-to-ste-code-generation"></a>貼り付けコード生成にスワップする

ここで、既定のコード生成を無効にし、自己追跡エンティティにスワップする必要があります。

### <a name="if-you-are-using-visual-studio-2012"></a>Visual Studio 2012 を使用している場合

-   **ソリューションエクスプローラー**で**bBloggingModel.tt**を展開**し、BloggingModel.Context.tt と
    ** を削除して、*既定のコード生成を無効*にします。
-   EF デザイナー画面で空の領域を右クリックし、 **[コード生成項目の追加...]** を選択します。
-   左ペインで **[オンライン]** を選択し、**貼り付け Generator**を検索します。
-   [**貼り付け Generator For C\#** ] テンプレートを選択し、名前として「 **stetemplate** 」と入力して、 **[追加]** をクリックします。
-   **STETemplate.tt**ファイルと**STETemplate.Context.tt**ファイルは、b ファイルの下に入れ子になっています。

### <a name="if-you-are-using-visual-studio-2010"></a>Visual Studio 2010 を使用している場合

-   EF デザイナー画面で空の領域を右クリックし、 **[コード生成項目の追加...]** を選択します。
-   左ペインで **[コード]** を選択し、 **[ADO.NET エンティティジェネレーター]** をクリックします。
-   名前として「 **Stetemplate** 」と入力し、 **[追加]** をクリックします。
-   **STETemplate.tt**ファイルと**STETemplate.Context.tt**ファイルがプロジェクトに直接追加されます。

## <a name="move-entity-types-into-separate-project"></a>エンティティ型を別のプロジェクトに移動する

自己追跡エンティティを使用するには、クライアントアプリケーションは、モデルから生成されたエンティティクラスにアクセスする必要があります。 モデル全体をクライアントアプリケーションに公開する必要がないため、エンティティクラスを別のプロジェクトに移動します。

最初の手順では、既存のプロジェクトでのエンティティクラスの生成を停止します。

-   **ソリューションエクスプローラー**の **[STETemplate.tt]** を右クリックし、 **[プロパティ]** を選択します。
-   **[プロパティ]** ウィンドウで、 **CustomTool**プロパティから**texttemplatingfilegenerator**をクリアします。
-   **ソリューションエクスプローラー**の **[STETemplate.tt]** を展開し、その下に入れ子になっているすべてのファイルを削除します。

次に、新しいプロジェクトを追加し、そこにエンティティクラスを生成します。

-   **ファイル&gt; の&gt; プロジェクトの追加...**
-   左側のウィンドウで [ **Visual C\#** ]、 **[クラスライブラリ]** の順に選択します。
-   名前として「STESample」と入力し、[ **OK]** をクリックし**ます。**
-   **プロジェクト-&gt; 既存の項目の追加...**
-   **STESample**プロジェクトフォルダーに移動します。
-   すべてのファイルを表示する場合に選択し**ます (\*\*)**
-   **STETemplate.tt**ファイルを選択します
-   **[追加]** ボタンの横にあるドロップダウン矢印をクリックし、 **[リンクとして追加]** を選択します。

    ![リンクテンプレートの追加](~/ef6/media/addlinkedtemplate.png)

また、エンティティクラスがコンテキストと同じ名前空間で生成されるようにします。 これにより、アプリケーション全体で追加する必要があるステートメントの数が減ります。

-   **ソリューションエクスプローラー**のリンクされた**STETemplate.tt**を右クリックし、 **[プロパティ]** を選択します。
-   **[プロパティ]** ウィンドウで、 **[カスタムツールの名前空間]** を**STESample**に設定します。

貼り付けテンプレートによって生成されるコードは、コンパイルする**ために、system.string への**参照を必要とします。 このライブラリは、シリアル化可能なエンティティ型で使用される WCF **DataContract**および**DataMember**属性に必要です。

-   **ソリューションエクスプローラー**で**STESample**プロジェクトを右クリックし、[参照の**追加**] を選択します。
    -   Visual Studio 2012-system.string の横にあるチェックボックスをオンにし、 **OK** をクリックし**ます。**
    -   Visual Studio 2010-system.string を選択し、 **OK** をクリックし**ます。**

最後に、コンテキストが含まれているプロジェクトには、エンティティ型への参照が必要です。

-   **ソリューションエクスプローラー**で**STESample**プロジェクトを右クリックし、 **[参照の追加]** を選択します。
    -   Visual Studio 2012-左側のウィンドウから **[ソリューション]** を選択し、STESample の横にあるチェックボックスをオンにして、 **[OK]** をクリックし**ます。**
    -   Visual Studio 2010- **[プロジェクト]** タブを選択し、STESample を選択して **[OK]** をクリックし**ます。**

>[!NOTE]
> エンティティ型を別のプロジェクトに移動するもう1つのオプションは、テンプレートファイルを既定の場所からリンクするのではなく、移動することです。 これを行う場合は、テンプレートの**inputFile**変数を更新して、edmx ファイルへの相対パスを指定する必要があります (この例では、 **..\\b**)。

## <a name="create-a-wcf-service"></a>WCF サービスを作成する

ここで、データを公開する WCF サービスを追加します。まず、プロジェクトを作成します。

-   **ファイル&gt; の&gt; プロジェクトの追加...**
-   左側のウィンドウで [ **Visual C\#** ] を選択し、 **[WCF サービスアプリケーション]** をクリックします。
-   名前として「STESample」と入力し、[ **OK]** をクリックし**ます。**
-   System.string アセンブリへの参照を追加**します。**
-   **STESample**プロジェクトおよび**STESample**プロジェクトへの参照を追加する

EF 接続文字列をこのプロジェクトにコピーして、実行時に検出されるようにする必要があります。

-    **STESample **プロジェクトの app.config ファイルを開き、 **connectionStrings**要素をコピーし**ます。**
-   **ConnectionStrings**要素を、STESample プロジェクトの**web.config ファイル**の**configuration**要素の子要素として貼り付け**ます。**

次に、実際のサービスを実装します。

-   **IService1.cs**を開き、内容を次のコードに置き換えます。

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

-   Service1 を開き、内容を次のコードに置き換え**ます。**

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

## <a name="consume-the-service-from-a-console-application"></a>コンソールアプリケーションからサービスを使用する

ここでは、サービスを使用するコンソールアプリケーションを作成します。

-   **ファイル&gt; 新規&gt; プロジェクト...**
-   左側のウィンドウで [ **Visual C\#** ] を選択し、 **[コンソールアプリケーション]** をクリックします。
-   名前として「STESample」と入力し、[ **OK]** をクリックし**ます。**
-   **STESample**プロジェクトへの参照を追加する

WCF サービスへのサービス参照が必要です

-   **ソリューションエクスプローラー**で [ **STESample] テスト**プロジェクトを右クリックし、 **[サービス参照の追加]** を選択します。
-   **[検出]** をクリック
-   名前空間として「 **B、Service** 」と入力し、[ **OK]** をクリックします。

これで、サービスを使用するコードを記述できるようになりました。

-   **Program.cs** を開き、その内容を次のコードに置き換えます。

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

-   **ソリューションエクスプローラー**で STESample テストプロジェクトを右クリックし、[デバッグ] **&gt; [新しいインスタンスを開始**] を選択し**ます。**

アプリケーションの実行時に、次の出力が表示されます。

```console
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

## <a name="consume-the-service-from-a-wpf-application"></a>WPF アプリケーションからサービスを使用する

ここでは、サービスを使用する WPF アプリケーションを作成します。

-   **ファイル&gt; 新規&gt; プロジェクト...**
-   左側のウィンドウで [ **Visual C\#** ] を選択し、 **[WPF アプリケーション]** をクリックします。
-   名前として「 **STESample. WPFTest** 」と入力し、[ **OK]** をクリックします。
-   **STESample**プロジェクトへの参照を追加する

WCF サービスへのサービス参照が必要です

-   **ソリューションエクスプローラー**で**STESample**プロジェクトを右クリックし、 **[サービス参照の追加]** を選択します。
-   **[検出]** をクリック
-   名前空間として「 **B、Service** 」と入力し、[ **OK]** をクリックします。

これで、サービスを使用するコードを記述できるようになりました。

-   **Mainwindow.xaml**を開き、内容を次のコードに置き換えます。

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

-   Mainwindow.xaml (**MainWindow.xaml.cs**) の分離コードを開き、内容を次のコードに置き換えます。

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

-   **ソリューションエクスプローラー**で STESample プロジェクトを右クリックし、[デバッグ] **&gt; [新しいインスタンスを開始**] を選択し**ます。**
-   画面を使用してデータを操作し、 **[保存]** ボタンを使用してサービスで保存することができます。

![WPF メインウィンドウ](~/ef6/media/wpf.png)
