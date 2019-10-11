---
title: Database First-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: cc6ffdb3-388d-4e79-a201-01ec2577c949
ms.openlocfilehash: d40cff4ddccf43a394ef4f244653372a5a89b05a
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182449"
---
# <a name="database-first"></a>Database First
このビデオとステップバイステップのチュートリアルでは、Entity Framework を使用した Database First 開発の概要について説明します。 Database First を使用すると、既存のデータベースからモデルをリバースエンジニアリングできます。 モデルは EDMX ファイル (.edmx 拡張子) に格納されており、Entity Framework Designer で表示および編集できます。 アプリケーションで対話するクラスは、EDMX ファイルから自動的に生成されます。

## <a name="watch-the-video"></a>ビデオを見る
このビデオでは、Entity Framework を使用した Database First 開発の概要について説明します。 Database First を使用すると、既存のデータベースからモデルをリバースエンジニアリングできます。 モデルは EDMX ファイル (.edmx 拡張子) に格納されており、Entity Framework Designer で表示および編集できます。 アプリケーションで対話するクラスは、EDMX ファイルから自動的に生成されます。

**表示者**:[Rowan 明美](https://romiller.com/)

**ビデオ**:[WMV](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv) | [MP4](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)

## <a name="pre-requisites"></a>前提条件

このチュートリアルを完了するには、少なくとも Visual Studio 2010 または Visual Studio 2012 がインストールされている必要があります。

Visual Studio 2010 を使用している場合は、 [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)もインストールする必要があります。

 

## <a name="1-create-an-existing-database"></a>1. 既存のデータベースを作成する

通常、既存のデータベースを対象とする場合は、既に作成されますが、このチュートリアルでは、にアクセスするデータベースを作成する必要があります。

Visual Studio と共にインストールされるデータベースサーバーは、インストールされている Visual Studio のバージョンによって異なります。

-   Visual Studio 2010 を使用している場合は、SQL Express データベースを作成します。
-   Visual Studio 2012 を使用している場合は、 [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx)データベースを作成します。

 

では、データベースを生成してみましょう。

-   Visual Studio を開く
-   **ビュー-&gt; サーバーエクスプローラー**
-   [データ接続] を右クリックし **、&gt; [接続の追加**] をクリックします。
-   サーバーエクスプローラーからデータベースに接続していない場合は、データソースとして Microsoft SQL Server を選択する必要があります。

    ![データソースの選択](~/ef6/media/selectdatasource.png)

-   インストールされているものに応じて LocalDB または SQL Express に接続し、データベース名として「Databasefirst」と入力し**ます。**

    ![Sql Express 接続 DF](~/ef6/media/sqlexpressconnectiondf.png)

    ![LocalDB 接続 DF](~/ef6/media/localdbconnectiondf.png)

-   [ **OK]** を選択すると、新しいデータベースを作成するかどうかを確認するメッセージが表示されます。 **[はい]** を選択します。

    ![データベースの作成ダイアログ](~/ef6/media/createdatabasedialog.png)

-   新しいデータベースがサーバーエクスプローラーに表示され、右クリックして **[新しいクエリ]** を選択します。
-   次の SQL を新しいクエリにコピーし、クエリを右クリックして、 **[実行]** を選択します。

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
```

## <a name="2-create-the-application"></a>2. アプリケーションを作成する

単純にするために、Database First を使用してデータアクセスを実行する基本的なコンソールアプリケーションを構築します。

-   Visual Studio を開く
-   **ファイル-&gt; 新規-@no__t プロジェクト...**
-   左側のメニューと**コンソールアプリケーション**から **[Windows]** を選択します。
-   名前として「 **Databasefirstsample** 」と入力します。
-   **[OK]** を選択します。

 

## <a name="3-reverse-engineer-model"></a>3.リバースエンジニアリングモデル

ここでは、Visual Studio の一部として含まれている Entity Framework Designer を使用して、モデルを作成します。

-   **プロジェクト-&gt; 新しい項目の追加...**
-   左側のメニューから **データ** を選択し、ADO.NET をクリックし**Entity Data Model**
-   名前として「 **Bのログインモデル**」と入力し、[ **OK]** をクリックします。
-   **Entity Data Model ウィザード**が起動します。
-   **[データベースから生成]** を選択し、 **[次へ]** をクリックします。

    ![ウィザードの手順 1.](~/ef6/media/wizardstep1.png)

-   最初のセクションで作成したデータベースへの接続を選択し、接続文字列の名前として「 **Bのログインコンテキスト**」と入力して、 **[次へ]** をクリックします。

    ![ウィザードの手順 2.](~/ef6/media/wizardstep2.png)

-   [テーブル] の横にあるチェックボックスをオンにしてすべてのテーブルをインポートし、[完了] をクリックします。

    ![ウィザードの手順 3.](~/ef6/media/wizardstep3.png)

 

リバースエンジニアリングプロセスが完了すると、新しいモデルがプロジェクトに追加され、Entity Framework Designer で表示できるように開かれます。 App.config ファイルも、データベースの接続の詳細と共にプロジェクトに追加されています。

![モデルの初期](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a>Visual Studio 2010 の追加手順

Visual Studio 2010 で作業している場合、Entity Framework の最新バージョンにアップグレードするには、いくつかの追加の手順に従う必要があります。 アップグレードは、強化された API サーフェイスにアクセスできるようにするために重要です。これは、より使いやすく、最新のバグ修正にも使用できます。

まず、NuGet から最新バージョンの Entity Framework を取得する必要があります。

-   **プロジェクト-&gt; NuGet パッケージの管理...** 
     ***[nuget パッケージの管理...]** オプションがない場合は、[最新バージョンの nuget](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)をインストールする必要があり*ます。
-   **[オンライン]** タブを選択します。
-   **Entityframework**パッケージを選択します
-   **[インストール]** をクリックします。

次に、モデルをスワップして、新しいバージョンの Entity Framework で導入された DbContext API を使用するコードを生成する必要があります。

-   EF デザイナーでモデルの空の場所を右クリックし、 **[コード生成項目の追加...]** を選択します。
-   左側のメニューから **[オンラインテンプレート]** を選択し、 **dbcontext**を検索します。
-   **C @ no__t の EF 5.X DbContext ジェネレーター**を選択し、名前として「 **b model** 」と入力して、 **[追加]** をクリックします。

    ![DbContext テンプレート](~/ef6/media/dbcontexttemplate.png)

 

## <a name="4-reading--writing-data"></a>4。データの読み取り & 書き込み

モデルを作成したので、これを使用していくつかのデータにアクセスします。 データへのアクセスに使用するクラスは、EDMX ファイルに基づいて自動的に生成されます。

*このスクリーンショットは Visual Studio 2012 からのものです。 Visual Studio 2010 を使用している場合は、BloggingModel.tt ファイルと BloggingModel.Context.tt ファイルは、EDMX ファイルの下に入れ子になっているのではなく、プロジェクトの直下に配置されます。*

![生成されたクラス DF](~/ef6/media/generatedclassesdf.png)

 

次に示すように、Program.cs に Main メソッドを実装します。 このコードは、コンテキストの新しいインスタンスを作成し、それを使用して新しいブログを挿入します。 次に、LINQ クエリを使用して、データベースのすべてのブログをタイトル別にアルファベット順に並べ替えて取得します。

``` csharp
class Program
{
    static void Main(string[] args)
    {
        using (var db = new BloggingContext())
        {
            // Create and save a new Blog
            Console.Write("Enter a name for a new Blog: ");
            var name = Console.ReadLine();

            var blog = new Blog { Name = name };
            db.Blogs.Add(blog);
            db.SaveChanges();

            // Display all Blogs from the database
            var query = from b in db.Blogs
                        orderby b.Name
                        select b;

            Console.WriteLine("All blogs in the database:");
            foreach (var item in query)
            {
                Console.WriteLine(item.Name);
            }

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

これで、アプリケーションを実行してテストできるようになりました。

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
 

## <a name="5-dealing-with-database-changes"></a>5。データベースの変更に対処する

次に、データベーススキーマをいくつか変更します。これらの変更を行うときは、これらの変更を反映するようにモデルを更新する必要もあります。

最初の手順では、データベーススキーマにいくつかの変更を行います。 ここでは、ユーザーテーブルをスキーマに追加します。

-   サーバーエクスプローラーで**Databasefirst. ブログ**データベースを右クリックし、 **[新しいクエリ]** を選択します。
-   次の SQL を新しいクエリにコピーし、クエリを右クリックして、 **[実行]** を選択します。

``` SQL
CREATE TABLE [dbo].[Users]
(
    [Username] NVARCHAR(50) NOT NULL PRIMARY KEY,  
    [DisplayName] NVARCHAR(MAX) NULL
)
```

スキーマが更新されたので、これらの変更によってモデルを更新します。

-   EF デザイナーでモデルの空の場所を右クリックし、[データベースからモデルを更新...] を選択すると、更新ウィザードが起動します。
-   更新ウィザードの [追加] タブで、[テーブル] の横にあるチェックボックスをオンにします。これは、スキーマから新しいテーブルを追加することを示します。
    *The タブには、更新中に変更がチェックされる、モデル内の既存のテーブルが表示されます。[削除] タブには、スキーマから削除されたテーブルが表示され、更新の一部としてモデルからも削除されます。これら2つのタブの情報は自動的に検出され、情報提供のみを目的として提供されます。*

    ![更新ウィザード](~/ef6/media/refreshwizard.png)

-   更新ウィザードで [完了] をクリックします。

 

これで、モデルが更新され、データベースに追加したユーザーテーブルにマップされる新しいユーザーエンティティが追加されました。

![更新されたモデル](~/ef6/media/modelupdated.png)

## <a name="summary"></a>まとめ

このチュートリアルでは Database First 開発について説明しました。これにより、既存のデータベースに基づいて EF デザイナーでモデルを作成できるようになりました。 次に、そのモデルを使用して、データベースのデータの読み取りと書き込みを行います。 最後に、データベーススキーマに加えられた変更を反映するようにモデルを更新しました。
