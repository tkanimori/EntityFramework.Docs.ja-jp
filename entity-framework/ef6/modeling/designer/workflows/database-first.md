---
title: データベースの最初の EF6
author: divega
ms.date: 2016-10-23
ms.assetid: cc6ffdb3-388d-4e79-a201-01ec2577c949
ms.openlocfilehash: c60108c09fcbaaa1f86e77fa52cb13fe018975e1
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995911"
---
# <a name="database-first"></a>最初のデータベースします。
このビデオ、およびステップ バイ ステップ チュートリアルでは、Entity Framework を使用して Database First の開発の概要を提供します。 データベース最初にすることができますをリバース エンジニア リング、既存のデータベースからモデル。 モデルは、EDMX ファイル (拡張子は .edmx) に格納され、表示および編集できます、Entity Framework デザイナーで。 アプリケーションと対話するクラスは、EDMX ファイルから自動的に生成されます。

## <a name="watch-the-video"></a>ビデオを見る
このビデオでは、Entity Framework を使用して Database First の開発の概要を提供します。 データベース最初にすることができますをリバース エンジニア リング、既存のデータベースからモデル。 モデルは、EDMX ファイル (拡張子は .edmx) に格納され、表示および編集できます、Entity Framework デザイナーで。 アプリケーションと対話するクラスは、EDMX ファイルから自動的に生成されます。

**提供**: [Rowan Miller](http://romiller.com/)

**ビデオ**: [WMV](http://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv) | [MP4](http://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)

## <a name="pre-requisites"></a>前提条件

少なくとも Visual Studio 2010 が必要がありますか、このチュートリアルを実行する Visual Studio 2012 がインストールされています。

Visual Studio 2010 を使用している場合も必要になりますが[NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)をインストールします。

 

## <a name="1-create-an-existing-database"></a>1.既存のデータベースを作成します。

通常それは既に作成されて、既存のデータベースを対象としているときに、このチュートリアルではアクセスするデータベースを作成する必要があります。

Visual Studio と共にインストールされているデータベース サーバーは、インストールした Visual Studio のバージョンによって異なります。

-   Visual Studio 2010 を使用している場合は、SQL Express データベースを作成するがします。
-   Visual Studio 2012 を使用しているかどうかは、作成する、 [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx)データベース。

 

データベースを生成してみましょう。

-   Visual Studio を開く
-   **ビュー -&gt;サーバー エクスプ ローラー**
-   右クリックして**データ接続 -&gt;接続の追加.**
-   場合、まだデータベースに接続して、サーバー エクスプ ローラーから前に、データ ソースとして Microsoft SQL Server を選択する必要があります。

    ![SelectDataSource](~/ef6/media/selectdatasource.png)

-   LocalDB または SQL Express をインストールしたものに応じてのいずれかに接続し、入力**DatabaseFirst.Blogging**データベース名として

    ![SqlExpressConnectionDF](~/ef6/media/sqlexpressconnectiondf.png)

    ![LocalDBConnectionDF](~/ef6/media/localdbconnectiondf.png)

-   選択**OK**かどうかは、新しいデータベースを作成するように要求がある **[はい]**

    ![CreateDatabaseDialog](~/ef6/media/createdatabasedialog.png)

-   新しいデータベースがサーバー エクスプ ローラーに表示されますを右クリックして選択**新しいクエリ**
-   新しいクエリに、次の SQL をコピーし、クエリを選択します右クリックし、 **Execute**

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

## <a name="2-create-the-application"></a>2.アプリケーションを作成する

単純化するには、Database First を使用して、データ アクセスを実行する基本的なコンソール アプリケーションをビルドしていきます。

-   Visual Studio を開く
-   **ファイル -&gt;新機能 -&gt;プロジェクト.**
-   選択**Windows**左側のメニューと**コンソール アプリケーション**
-   入力**DatabaseFirstSample**名として
-   **[OK]** を選択します。

 

## <a name="3-reverse-engineer-model"></a>3.リバース エンジニア リング モデル

Visual Studio の一部として含まれている Entity Framework デザイナーを使用してモデルを作成することになります。

-   **プロジェクト -&gt;新しい項目を追加しています.**
-   選択**データ**左側のメニューをクリックし**ADO.NET Entity Data Model**
-   入力**BloggingModel**名をクリックします**OK**
-   これにより、起動、 **Entity Data Model ウィザード**
-   選択**データベースから生成**クリック **[次へ]**

    ![WizardStep1](~/ef6/media/wizardstep1.png)

-   最初のセクションで作成したデータベース接続を選択して、入力**BloggingContext**をクリックして、接続文字列の名前として **[次へ]**

    ![WizardStep2](~/ef6/media/wizardstep2.png)

-   すべてのテーブルをインポートし、[完了] をクリックします。 'テーブル' の横にあるチェック ボックスをクリックします。

    ![WizardStep3](~/ef6/media/wizardstep3.png)

 

リバース エンジニア リング プロセスが完了すると、新しいモデルがプロジェクトに追加しを開いて、Entity Framework デザイナーで表示することです。 App.config ファイルが、データベースの接続詳細を含むプロジェクトにも追加されました。

![ModelInitial](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a>Visual Studio 2010 で追加の手順

Visual Studio 2010 で作業している場合は、いくつか追加の手順を次の Entity Framework の最新バージョンにアップグレードする必要があります。 それにアクセスできる最新のバグ修正と強化された API へのアクセスを簡単に使用されるため、アップグレードすることが重要です。

まず、NuGet から最新バージョンの Entity Framework を取得する必要があります。

-   **プロジェクト:&gt; NuGet パッケージを管理しています.** 
    *していない場合、 **NuGet パッケージの管理.** オプションをインストールする必要があります、 [NuGet の最新バージョン](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)*
-   選択、**オンライン** タブ
-   選択、 **EntityFramework**パッケージ
-   クリックして**インストール**

次に、Entity Framework の以降のバージョンで導入された DbContext API を使用するコードを生成するには、このモデルを交換する必要があります。

-   EF Designer でのモデルの空いている場所を右クリックして**コード生成項目の追加.**
-   選択**オンライン テンプレート**、左側のメニューと検索から**DbContext**
-   EF の選択**5.x C 用 DbContext ジェネレーター\#**、入力**BloggingModel**名をクリックします**追加**

    ![DbContextTemplate](~/ef6/media/dbcontexttemplate.png)

 

## <a name="4-reading--writing-data"></a>4.読み取りとデータの書き込み

使用して一部のデータにアクセスするには、モデルができました。 クラスは、ここでを使用するデータにアクセスされている自動的に生成される EDMX ファイルに基づいて。

*このスクリーン ショットは Visual Studio 2012、Visual Studio 2010 を使用している場合、BloggingModel.tt、BloggingModel.Context.tt ファイル、プロジェクトの下に直接ではなく EDMX ファイルの下で入れ子になった。*

![GeneratedClassesDF](~/ef6/media/generatedclassesdf.png)

 

次に示すように、Program.cs の Main メソッドを実装します。 このコードは、コンテキストの新しいインスタンスを作成、し、それを使用して、新しいブログを挿入します。 タイトルでアルファベット順では、データベースからすべてのブログを取得するのに LINQ クエリを使用します。

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

アプリケーションを実行し、テストを実行できるようになりました。

```
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
 

## <a name="5-dealing-with-database-changes"></a>5.データベースの変更を処理します。

ここもそれらの変更を反映するように、モデルを更新する必要があります、これらの変更を行った場合、データベース スキーマをいくつか変更します。

最初の手順は、データベース スキーマを一部変更するのには。 ユーザー テーブルのスキーマを追加しようとしています。

-   右クリックし、 **DatabaseFirst.Blogging**サーバー エクスプ ローラーでデータベースし、選択**新しいクエリ**
-   新しいクエリに、次の SQL をコピーし、クエリを選択します右クリックし、 **Execute**

``` SQL
CREATE TABLE [dbo].[Users]
(
    [Username] NVARCHAR(50) NOT NULL PRIMARY KEY,  
    [DisplayName] NVARCHAR(MAX) NULL
)
```

これで、スキーマを更新すると、それらの変更、モデルの更新に時間を勧めします。

-   EF デザイナーでモデルおよび選択 'からモデルを更新しています... データベース' の空いている場所を右クリックし、この更新プログラム ウィザードが起動
-   テーブルの横のボックスの更新ウィザードのチェックの追加 タブで、スキーマから新しいテーブルを追加することを示します。
    *[更新] タブでは、モデルの変更、更新中にチェックされる、既存のテーブルを示します。削除のタブは、スキーマから削除されているし、更新プログラムの一部として、モデルから削除もテーブルを表示します。これら 2 つのタブ上の情報は自動的に検出し、は情報提供だけを目的としており、設定を変更することはできません。*

    ![RefreshWizard](~/ef6/media/refreshwizard.png)

-   更新ウィザードで [完了] をクリックします。

 

モデルが更新され、データベースに追加するユーザー テーブルにマップする新しいユーザー エンティティを含めます。

![ModelUpdated](~/ef6/media/modelupdated.png)

## <a name="summary"></a>まとめ

このチュートリアルでは、Database First の開発を見たのでですることができます、既存のデータベースに基づく EF Designer でモデルを作成します。 そのモデルを使用して、データベースからいくつかのデータを読み書きします。 最後に、データベース スキーマに加えられた変更を反映するようにモデルを更新します。
