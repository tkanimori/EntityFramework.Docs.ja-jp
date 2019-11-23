---
title: Model First-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: e1b9c319-bb8a-4417-ac94-7890f257e7f6
ms.openlocfilehash: 1b37805beb3d33f0b6dad2577a8abb3ea8f7b1e4
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182439"
---
# <a name="model-first"></a>Model First
このビデオとステップバイステップのチュートリアルでは、Entity Framework を使用した Model First 開発の概要について説明します。 Model First を使用すると、Entity Framework Designer を使用して新しいモデルを作成し、モデルからデータベーススキーマを生成できます。 モデルは EDMX ファイル (.edmx 拡張子) に格納されており、Entity Framework Designer で表示および編集できます。 アプリケーションで対話するクラスは、EDMX ファイルから自動的に生成されます。

## <a name="watch-the-video"></a>ビデオを見る
このビデオとステップバイステップのチュートリアルでは、Entity Framework を使用した Model First 開発の概要について説明します。 Model First を使用すると、Entity Framework Designer を使用して新しいモデルを作成し、モデルからデータベーススキーマを生成できます。 モデルは EDMX ファイル (.edmx 拡張子) に格納されており、Entity Framework Designer で表示および編集できます。 アプリケーションで対話するクラスは、EDMX ファイルから自動的に生成されます。

**提供**: [Rowan Miller](https://romiller.com/)

**ビデオ**: [wmv](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.wmv) | [MP4](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v) | [wmv (ZIP)](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)

## <a name="pre-requisites"></a>前提条件

このチュートリアルを完了するには、Visual Studio 2010 または Visual Studio 2012 がインストールされている必要があります。

Visual Studio 2010 を使用している場合は、 [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)もインストールする必要があります。

## <a name="1-create-the-application"></a>1. アプリケーションを作成する

単純にするために、Model First を使用してデータアクセスを実行する基本的なコンソールアプリケーションを構築します。

-   Visual Studio を開きます
-   **ファイル&gt; 新規&gt; プロジェクト...**
-   左側のメニューと**コンソールアプリケーション**から **[Windows]** を選択します。
-   名前として**Modelfirstsample**を入力します
-   **[OK]** を選択します。

## <a name="2-create-model"></a>2. モデルを作成する

ここでは、Visual Studio の一部として含まれている Entity Framework Designer を使用して、モデルを作成します。

-   **プロジェクト-新しい項目の追加&gt;...**
-   左側のメニューから **データ** を選択し、ADO.NET をクリックし**Entity Data Model**
-   名前として「 **Bloggingmodel** 」と入力し、 **[OK]** をクリックすると、Entity Data Model ウィザードが起動します。
-   **空のモデル**を選択し、 **[完了]** をクリックします

    ![空のモデルの作成](~/ef6/media/createemptymodel.png)

空のモデルを使用して Entity Framework Designer が開かれています。 これで、モデルへのエンティティ、プロパティ、およびアソシエーションの追加を開始できます。

-   デザイン画面を右クリックし、 **[プロパティ]** を選択します。
-   このプロパティウィンドウでは、**エンティティコンテナー名**を**b
    コンテキスト**に変更します。*これは、生成される派生コンテキストの名前です。コンテキストは、データベースとのセッションを表し、データのクエリと保存を可能*にします。
-   デザイン画面を右クリックし、[ **Add New-&gt; エンティティ**] を選択します。
-   エンティティ名として「ブログ」を、キー名として **「** **Blog** **id** 」を入力し、[OK] をクリックします。

    ![ブログエンティティの追加](~/ef6/media/addblogentity.png)

-   デザイン画面で新しいエンティティを右クリックし、[**新しい&gt; スカラープロパティの追加**] を選択し、プロパティの名前として「 **name** 」と入力します。
-   **Url**プロパティを追加するには、この手順を繰り返します。
-   デザイン画面で **[url]** プロパティを右クリックし、 **[プロパティ]** を選択します。 プロパティウィンドウ  **[null 許容]** の設定を **[True]** に変更し
    *ブログを Url に割り当てずにデータベースに保存できる*ようにします。
-   習得した手法を使用して、 **PostId** key プロパティを持つ**Post**エンティティを追加します。
-   **Post**エンティティに**タイトル**と**コンテンツ**のスカラープロパティを追加する

いくつかのエンティティを作成したので、次はそれらの間にアソシエーション (またはリレーションシップ) を追加します。

-   デザインサーフェイスを右クリックし、[追加] [**新しい関連付け] の**順に選択し&gt;
-   リレーションシップの1つの端を、多重度が**1**で、もう一方のエンドポイントが**多数**の
    の複数要素の接続性**を持つ** **ブログ**になるようにします。*これは、ブログに多数の投稿があり、投稿が1つのブログに属していることを意味*します。
-   **[外部キーのプロパティを ' Post ' エンティティに追加する]** チェックボックスがオンになっていることを確認し、 **[OK]** をクリックします。

    ![関連付けの追加 (MF)](~/ef6/media/addassociationmf.png)

ここでは、データベースを生成してデータの読み書きに使用できる単純なモデルを用意しました。

![モデルの初期](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a>Visual Studio 2010 の追加手順

Visual Studio 2010 で作業している場合、Entity Framework の最新バージョンにアップグレードするには、いくつかの追加の手順に従う必要があります。 アップグレードは、強化された API サーフェイスにアクセスできるようにするために重要です。これは、より使いやすく、最新のバグ修正にも使用できます。

まず、NuGet から最新バージョンの Entity Framework を取得する必要があります。

-   **プロジェクト–&gt; NuGet パッケージの管理...** 
     ***[nuget パッケージの管理...]** オプションがない場合は、[最新バージョンの nuget](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)をインストールする必要があり*ます。
-   **[オンライン]** タブを選択します。
-   **Entityframework**パッケージを選択します
-   **[インストール]** をクリックします。

次に、モデルをスワップして、新しいバージョンの Entity Framework で導入された DbContext API を使用するコードを生成する必要があります。

-   EF デザイナーでモデルの空の場所を右クリックし、 **[コード生成項目の追加...]** を選択します。
-   左側のメニューから **[オンラインテンプレート]** を選択し、 **dbcontext**を検索します。
-   **C\#の EF 5.X DbContext ジェネレーター**を選択し、名前として「 **bを**使用したモデル」と入力して **[追加]** をクリックします。

    ![DbContext テンプレート](~/ef6/media/dbcontexttemplate.png)

## <a name="3-generating-the-database"></a>3. データベースを生成する

モデルを使用すると、Entity Framework は、モデルを使用してデータの格納と取得を行うことができるデータベーススキーマを計算できます。

Visual Studio と共にインストールされるデータベースサーバーは、インストールされている Visual Studio のバージョンによって異なります。

-   Visual Studio 2010 を使用している場合は、SQL Express データベースを作成します。
-   Visual Studio 2012 を使用している場合は、 [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx)データベースを作成します。

では、データベースを生成してみましょう。

-   デザイン画面を右クリックし、 **[モデルからデータベースを生成...]** を選択します。
-   **[新しい接続]** をクリックします。 使用している Visual Studio のバージョンに応じて、LocalDB または SQL Express のいずれかを指定し、データベース名として「 **Modelfirst** 」と入力します。

    ![LocalDB 接続の MF](~/ef6/media/localdbconnectionmf.png)

    ![Sql Express 接続の MF](~/ef6/media/sqlexpressconnectionmf.png)

-   [ **OK]** を選択すると、新しいデータベースを作成するかどうかを確認するメッセージが表示されます。 **[はい]** を選択します。
-   **[次へ]** を選択すると、データベーススキーマを作成するスクリプトが Entity Framework Designer によって計算されます。
-   スクリプトが表示されたら、 **[完了]** をクリックすると、スクリプトがプロジェクトに追加されて開かれます。
-   スクリプトを右クリックして **[実行]** を選択すると、接続先のデータベースを指定するように求められます。使用している Visual Studio のバージョンに応じて、LocalDB または SQL Server Express を指定します。

## <a name="4-reading--writing-data"></a>4. データの読み取り & 書き込み

モデルを作成したので、これを使用していくつかのデータにアクセスします。 データへのアクセスに使用するクラスは、EDMX ファイルに基づいて自動的に生成されます。

*このスクリーンショットは Visual Studio 2012 からのものです。 Visual Studio 2010 を使用している場合は、BloggingModel.tt ファイルと BloggingModel.Context.tt ファイルは、EDMX ファイルの下に入れ子になっているのではなく、プロジェクトの直下に配置されます。*

![生成されたクラス](~/ef6/media/generatedclasses.png)

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

## <a name="5-dealing-with-model-changes"></a>5. モデルの変更を処理する

次に、モデルに何らかの変更を加えてみましょう。これらの変更を行うときは、データベーススキーマも更新する必要があります。

まず、モデルに新しいユーザーエンティティを追加します。

-   キーの名前と**文字列**としてユーザー**名**を持つ新しい**ユーザー**エンティティ名を、キーのプロパティの型として追加します。

    ![ユーザーエンティティの追加](~/ef6/media/adduserentity.png)

-   デザイン画面で **[ユーザー名]** プロパティを右クリックし、 **[プロパティ**] を選択します。プロパティウィンドウ、 **[MaxLength]** 設定を**50**に変更し
    これによって、*ユーザー名に格納できるデータが50文字に制限さ*れます。
-   **DisplayName**スカラープロパティを**User**エンティティに追加する

これで、モデルが更新され、新しいユーザーエンティティ型に対応するようにデータベースを更新する準備ができました。

-   デザイン画面を右クリックし、 **[モデルからデータベースを生成]** を選択すると、Entity Framework によって、更新されたモデルに基づいてスキーマを再作成するスクリプトが計算されます。
-   **[完了]** をクリック
-   既存の DDL スクリプトとモデルのマッピングおよびストレージ部分の上書きに関する警告が表示される場合は、両方の警告に対して **[はい]** をクリックします。
-   データベースを作成するための更新された SQL スクリプトが開きます  
    *生成されたスクリプトは、既存のすべてのテーブルを削除してから、スキーマを最初から再作成します。これは、ローカルでの開発では機能しますが、既に配置されているデータベースに変更をプッシュする場合には有効ではありません。既に配置されているデータベースに変更を発行する必要がある場合は、スクリプトを編集するか、スキーマ比較ツールを使用して移行スクリプトを計算する必要があります。*
-   スクリプトを右クリックして **[実行]** を選択すると、接続先のデータベースを指定するように求められます。使用している Visual Studio のバージョンに応じて、LocalDB または SQL Server Express を指定します。

## <a name="summary"></a>要約

このチュートリアルでは Model First の開発について説明しました。これにより、EF デザイナーでモデルを作成し、そのモデルからデータベースを生成することができました。 次に、モデルを使用して、データベースのデータの読み取りと書き込みを行います。 最後に、モデルを更新した後、モデルに一致するようにデータベーススキーマを再作成しました。
