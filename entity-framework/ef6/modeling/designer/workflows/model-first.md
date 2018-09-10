---
title: 最初に EF6 をモデル化します。
author: divega
ms.date: 2016-10-23
ms.assetid: e1b9c319-bb8a-4417-ac94-7890f257e7f6
ms.openlocfilehash: 3dd0eba29619f09995d7009dd29462c14bde98c4
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2018
ms.locfileid: "44251142"
---
# <a name="model-first"></a>まずモデルします。
このビデオ、およびステップ バイ ステップ チュートリアルでは、Entity Framework を使用して Model First の開発の概要を提供します。 まず、モデルを使用すると、Entity Framework デザイナーを使用して、新しいモデルを作成し、モデルからデータベース スキーマを生成できます。 モデルは、EDMX ファイル (拡張子は .edmx) に格納され、表示および編集できます、Entity Framework デザイナーで。 アプリケーションと対話するクラスは、EDMX ファイルから自動的に生成されます。

## <a name="watch-the-video"></a>ビデオを見る
このビデオ、およびステップ バイ ステップ チュートリアルでは、Entity Framework を使用して Model First の開発の概要を提供します。 まず、モデルを使用すると、Entity Framework デザイナーを使用して、新しいモデルを作成し、モデルからデータベース スキーマを生成できます。 モデルは、EDMX ファイル (拡張子は .edmx) に格納され、表示および編集できます、Entity Framework デザイナーで。 アプリケーションと対話するクラスは、EDMX ファイルから自動的に生成されます。

**提供**: [Rowan Miller](http://romiller.com/)

**ビデオ**: [WMV](http://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.wmv) | [MP4](http://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)

## <a name="pre-requisites"></a>前提条件

Visual Studio 2010 を使用する必要がありますか、このチュートリアルを実行する Visual Studio 2012 がインストールされています。

Visual Studio 2010 を使用している場合も必要になりますが[NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)をインストールします。

## <a name="1-create-the-application"></a>1.アプリケーションを作成する

複雑にならない Model First を使用して、データ アクセスを実行する基本的なコンソール アプリケーションをビルドしていきます。

-   Visual Studio を開く
-   **ファイル -&gt;新機能 -&gt;プロジェクト.**
-   選択**Windows**左側のメニューと**コンソール アプリケーション**
-   入力**ModelFirstSample**名として
-   **[OK]** を選択します。

## <a name="2-create-model"></a>2.モデルを作成します。

Visual Studio の一部として含まれている Entity Framework デザイナーを使用してモデルを作成することになります。

-   **プロジェクト -&gt;新しい項目を追加しています.**
-   選択**データ**左側のメニューをクリックし**ADO.NET Entity Data Model**
-   入力**BloggingModel**名をクリックします**OK**、Entity Data Model ウィザードが起動
-   選択**空のモデル**クリック**完了**

    ![空のモデルを作成します。](~/ef6/media/createemptymodel.png)

Entity Framework のデザイナーは、空のモデルで開かれます。 今すぐ、モデルにエンティティ、プロパティ、およびアソシエーションを追加するを開始できます。

-   デザイン サーフェスと選択を右クリックして**プロパティ**
-   プロパティ ウィンドウの変更で、**エンティティ コンテナー名**に**BloggingContext**
    *コンテキストを生成される派生コンテキストの名前ですクエリを実行し、データを保存することができます、データベースとのセッションを表します*
-   デザイン サーフェスと選択を右クリックして **- 新しい追加&gt;エンティティ.**
-   入力**ブログ**エンティティ名としてと**BlogId**キー名をクリックします**OK**

    ![ブログのエンティティを追加します。](~/ef6/media/addblogentity.png)

-   デザイン サーフェスと選択の新しいエンティティを右クリックして **- 新しい追加&gt;スカラー プロパティ**、入力**名前**プロパティの名前として。
-   追加するには、このプロセスを繰り返して、 **Url**プロパティ。
-   右クリックし、 **Url**プロパティをデザイン画面を選択します**プロパティ**、プロパティ ウィンドウの変更で、 **Nullable**設定**をTrue**
    *これにより、Url を代入せず、ブログをデータベースに保存するには*
-   追加習得する手法を使用して、 **Post**を持つエンティティを**投稿 Id**キー プロパティ
-   追加**タイトル**と**コンテンツ**スカラー プロパティを**Post**エンティティ

あるので、いくつかのエンティティ、それらの間の関連付け (またはリレーションシップ) を追加する時間を勧めします。

-   デザイン サーフェスと選択を右クリックして **- 新しい追加&gt;関連しています.**
-   リレーションシップの一方の端を指すように**ブログ**の多重度を持つ**1 つ**およびその他の終点を**Post**の多重度を持つ**多く** 
    *つまりブログは多くの投稿、および、1 つのブログに投稿が属しています。*
-   確認、 **'Post' エンティティに外部キー プロパティを追加**ボックスがオンになってし、 **OK**

    ![MF の関連付けを追加します。](~/ef6/media/addassociationmf.png)

データベースを生成してデータを読み書きするを使用して、単純なモデルがあるようになりました。

![初期モデルします。](~/ef6/media/modelinitial.png)

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

    ![DbContext テンプレート](~/ef6/media/dbcontexttemplate.png)

## <a name="3-generating-the-database"></a>3.データベースの生成

私たちのモデルを指定するには、Entity Framework は、データ モデルを使用して格納および取得することを許可するデータベース スキーマを計算できます。

Visual Studio と共にインストールされているデータベース サーバーは、インストールした Visual Studio のバージョンによって異なります。

-   Visual Studio 2010 を使用している場合は、SQL Express データベースを作成するがします。
-   Visual Studio 2012 を使用しているかどうかは、作成する、 [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx)データベース。

データベースを生成してみましょう。

-   デザイン サーフェスと選択を右クリックして**モデルからデータベースを生成しています.**
-   クリックして**新しい接続.** LocalDB または SQL Express は、Visual Studio のバージョンに応じて使用しているのいずれかを指定して入力**ModelFirst.Blogging**としてデータベース名。

    ![LocalDB 接続 MF](~/ef6/media/localdbconnectionmf.png)

    ![Sql Express 接続 MF](~/ef6/media/sqlexpressconnectionmf.png)

-   選択**OK**かどうかは、新しいデータベースを作成するように要求がある **[はい]**
-   選択**次**と、Entity Framework デザイナーでは、データベース スキーマを作成するスクリプトを計算します。
-   スクリプトが表示されたら、クリックして**完了**とスクリプトがプロジェクトに追加し、開く
-   クリックし、スクリプトを右クリックして**Execute**LocalDB を指定するのには、接続するデータベースを指定を求め、または SQL Server Express は、Visual Studio のバージョンに応じて使用しています。

## <a name="4-reading--writing-data"></a>4.読み取りとデータの書き込み

使用して一部のデータにアクセスするには、モデルができました。 クラスは、ここでを使用するデータにアクセスされている自動的に生成される EDMX ファイルに基づいて。

*このスクリーン ショットは Visual Studio 2012、Visual Studio 2010 を使用している場合、BloggingModel.tt、BloggingModel.Context.tt ファイル、プロジェクトの下に直接ではなく EDMX ファイルの下で入れ子になった。*

![生成されたクラス](~/ef6/media/generatedclasses.png)

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

## <a name="5-dealing-with-model-changes"></a>5.モデルの変更を処理します。

ここには、データベース スキーマを更新する必要もありますこれらの変更を行ったときに、モデルでは、変更を加えます。

私たちのモデルに新しいユーザー エンティティを追加することから始めます。

-   新しい追加**ユーザー**でエンティティ名**Username** 、キー名としてと**文字列**キーのプロパティの型として

    ![ユーザー エンティティを追加します。](~/ef6/media/adduserentity.png)

-   右クリックし、 **Username**プロパティをデザイン画面を選択します**プロパティ**のプロパティ ウィンドウの変更、 **MaxLength**設定**50** 
    *これは 50 文字にユーザー名で格納できるデータを制限*
-   追加、 **DisplayName**スカラー プロパティを**ユーザー**エンティティ

今すぐ更新済みのモデルがあるし、新しいユーザー エンティティ型の対応するためにデータベースを更新する準備ができました。

-   デザイン サーフェスと選択を右クリックして**モデルからデータベースを生成しています.**、Entity Framework は、更新されたモデルに基づくスキーマを再作成するスクリプトを計算します。
-   クリックして**完了**
-   既存の DDL スクリプトとモデルのマッピングと記憶域部分の上書きに関する警告が表示される、 をクリックする可能性があります**はい**これら両方の警告
-   ためにデータベースを作成する更新された SQL スクリプトを開く  
    *生成されるスクリプトでは、すべての既存のテーブルをドロップしを最初からスキーマを再作成します。これにより、ローカル開発用動作可能性がありますが、既にデプロイされているデータベースに変更をプッシュの実行可能なではありません。既にデプロイされているデータベースに変更を発行する必要がある場合は、スクリプトを編集またはスキーマ比較ツールを使用して、移行スクリプトを計算する必要があります。*
-   クリックし、スクリプトを右クリックして**Execute**LocalDB を指定するのには、接続するデータベースを指定を求め、または SQL Server Express は、Visual Studio のバージョンに応じて使用しています。

## <a name="summary"></a>まとめ

このチュートリアルでは、Model First の開発を見たのでですることができますが EF デザイナーでモデルを作成し、そのモデルからデータベースを生成します。 モデルを使用して、データベースからいくつかのデータを読み書きします。 最後に、モデルを更新し、モデルと一致するデータベース スキーマを再作成します。
