---
title: エンティティ デザイナーの分割の EF6
author: divega
ms.date: 10/23/2016
ms.assetid: aa2dd48a-1f0e-49dd-863d-d6b4f5834832
ms.openlocfilehash: ba1895ae491cec909ff88a8784eea82f1876f595
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490853"
---
# <a name="designer-entity-splitting"></a>デザイナーのエンティティを分割
このチュートリアルでは、Entity Framework デザイナー (EF Designer) を持つモデルを変更することで、エンティティ型を 2 つのテーブルにマップする方法を示します。 エンティティを複数のテーブルにマップできるのは、それらのテーブルのキーが共通している場合です。 エンティティ型を 2 つのテーブルにマップする場合に適用される概念は、エンティティ型を 3 つ以上のテーブルにマップする場合にも簡単に応用できます。

次の図は、EF Designer を使用する場合に使用される主なウィンドウを示します。

![EF デザイナー](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a>必須コンポーネント

Visual Studio 2012 または Visual Studio 2010、Ultimate、Premium、Professional、または Web Express のエディションです。

## <a name="create-the-database"></a>データベースを作成します。

Visual Studio と共にインストールされているデータベース サーバーは、インストールした Visual Studio のバージョンによって異なります。

-   Visual Studio 2012 を使用している場合、作成する LocalDB データベース。
-   Visual Studio 2010 を使用している場合は、SQL Express データベースを作成するがします。

まずここでは結合する 1 つのエンティティに 2 つのテーブルでデータベースを作成します。

-   Visual Studio を開く
-   **ビュー -&gt;サーバー エクスプ ローラー**
-   右クリックして**データ接続 -&gt;接続の追加.**
-   まだデータベースに接続して、サーバー エクスプ ローラーから選択する必要があります前に場合**Microsoft SQL Server**データ ソースとして
-   LocalDB または SQL Express をインストールしたものによってのいずれかに接続します。
-   入力**EntitySplitting**データベース名として
-   選択**OK**かどうかは、新しいデータベースを作成するように要求がある **[はい]**
-   新しいデータベースがサーバー エクスプ ローラーに表示されます。
-   Visual Studio 2012 を使用している場合
    -   サーバー エクスプ ローラーでデータベースを右クリックし、選択**新しいクエリ**
    -   新しいクエリに、次の SQL をコピーし、クエリを選択します右クリックし、 **Execute**
-   Visual Studio 2010 を使用している場合
    -   選択**データ -&gt; Transact SQL エディター -&gt;新しいクエリ接続しています.**
    -   入力 **.\\SQLEXPRESS**サーバー名をクリックします**OK**
    -   選択、 **EntitySplitting**クエリ エディターの上部にある下のドロップダウンからデータベース
    -   新しいクエリに、次の SQL をコピーし、クエリを選択します右クリックし、 **SQL の実行**

``` SQL
CREATE TABLE [dbo].[Person] (
[PersonId] INT IDENTITY (1, 1) NOT NULL,
[FirstName] NVARCHAR (200) NULL,
[LastName] NVARCHAR (200) NULL,
CONSTRAINT [PK_Person] PRIMARY KEY CLUSTERED ([PersonId] ASC)
);

CREATE TABLE [dbo].[PersonInfo] (
[PersonId] INT NOT NULL,
[Email] NVARCHAR (200) NULL,
[Phone] NVARCHAR (50) NULL,
CONSTRAINT [PK_PersonInfo] PRIMARY KEY CLUSTERED ([PersonId] ASC),
CONSTRAINT [FK_Person_PersonInfo] FOREIGN KEY ([PersonId]) REFERENCES [dbo].[Person] ([PersonId]) ON DELETE CASCADE
);
```

## <a name="create-the-project"></a>プロジェクトの作成

-   **[ファイル]** メニューの **[新規作成]** をポイントし、 **[プロジェクト]** をクリックします。
-   左側のウィンドウで次のようにクリックします。 **Visual C\#** を選び、**コンソール アプリケーション**テンプレート。
-   入力**MapEntityToTablesSample**をクリックして、プロジェクトの名前として**OK**します。
-   クリックして**いいえ**場合は、最初のセクションで作成した SQL クエリを保存するように求められます。

## <a name="create-a-model-based-on-the-database"></a>データベースに基づくモデルを作成します。

-   ソリューション エクスプ ローラーでプロジェクト名を右クリックし、[**追加**、] をクリックし、**新しい項目の**します。
-   選択**データ**選択し、左側のメニューから**ADO.NET Entity Data Model**テンプレート ペインでします。
-   入力**MapEntityToTablesModel.edmx**のファイル名、およびクリック**追加**します。
-   モデルのコンテンツの選択 ダイアログ ボックスで、次のように選択します。**データベースから生成**、 をクリックし、 **次へ。**
-   選択、 **EntitySplitting**ドロップダウンからの接続がダウンし、をクリックして**次**します。
-   データベース オブジェクトの選択 ダイアログ ボックスで、ボックスを横にチェック、**テーブル**ノード。
    これからのすべてのテーブルに追加されます、 **EntitySplitting**モデルへのデータベース。
-   **[完了]** をクリックします。

モデルを編集するため、デザイン サーフェイスを提供するエンティティ デザイナーが表示されます。

## <a name="map-an-entity-to-two-tables"></a>エンティティを 2 つのテーブルにマップします。

この手順では更新、 **Person**からデータを結合するエンティティ型、 **Person**と**PersonInfo**テーブル。

-   選択、**電子メール**と**Phone**のプロパティ、** PersonInfo ** エンティティ キーを押します**Ctrl + X**キー。
-   選択、* * ユーザー * * エンティティ キーを押します**Ctrl + V**キー。
-   デザイン サーフェイスでは、選択、 **PersonInfo**エンティティ キーを押します**削除**キーボードのキー。
-   クリックして**いいえ**を削除するように求められたら、 **PersonInfo**テーブルにマップしようとしていますが、モデルから、**人**エンティティ。

    ![テーブルを削除します。](~/ef6/media/deletetables.png)

次の手順が必要です、**マッピングの詳細**ウィンドウ。 このウィンドウを表示されない場合は、サーフェスと選択のデザインを右クリックして**マッピングの詳細**します。

-   選択、 **Person**エンティティの種類をクリックします **&lt;テーブルまたはビューの追加&gt;** で、**マッピングの詳細**ウィンドウ。
-   選択 * * PersonInfo * * - ドロップダウン リストから。
    **マッピングの詳細**ウィンドウが更新され、既定の列マッピング、これらは、ここでは問題ありません。

**Person**エンティティ型が現在にマップされている、 **Person**と**PersonInfo**テーブル。

![2 のマッピング](~/ef6/media/mapping2.png)

## <a name="use-the-model"></a>モデルを使用します。

-   Main メソッドでは、次のコードを貼り付けます。

``` csharp
    using (var context = new EntitySplittingEntities())
    {
        var person = new Person
        {
            FirstName = "John",
            LastName = "Doe",
            Email = "john@example.com",
            Phone = "555-555-5555"
        };

        context.People.Add(person);
        context.SaveChanges();

        foreach (var item in context.People)
        {
            Console.WriteLine(item.FirstName);
        }
    }
```

-   アプリケーションをコンパイルして実行します。

次の T-SQL ステートメントは、このアプリケーションを実行した結果、データベースに対して実行されました。 

-   次の 2 つ**挿入**ステートメントのコンテキストを実行した結果として実行します。SaveChanges() します。 データを取得する、 **Person**エンティティの間で分割し、 **Person**と**PersonInfo**テーブル。

    ![1 を挿入します。](~/ef6/media/insert1.png)

    ![2 を挿入します。](~/ef6/media/insert2.png)
-   次**選択**データベース内のユーザーの列挙の結果として実行されました。 データを組み合わせて、 **Person**と**PersonInfo**テーブル。

    ![選択](~/ef6/media/select.png)
