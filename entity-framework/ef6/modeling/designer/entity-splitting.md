---
title: デザイナーエンティティ分割-EF6
description: Entity Framework 6 でのデザイナーエンティティの分割
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/entity-splitting
ms.openlocfilehash: 8fdb9037fc49045089016e6630316d05c8726ad8
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064901"
---
# <a name="designer-entity-splitting"></a>デザイナーエンティティの分割
このチュートリアルでは、Entity Framework Designer (EF Designer) を使用してモデルを変更することによって、エンティティ型を2つのテーブルにマップする方法について説明します。 エンティティを複数のテーブルにマップできるのは、それらのテーブルのキーが共通している場合です。 エンティティ型を 2 つのテーブルにマップする場合に適用される概念は、エンティティ型を 3 つ以上のテーブルにマップする場合にも簡単に応用できます。

次の図は、EF デザイナーを操作するときに使用されるメインウィンドウを示しています。

![EF デザイナー](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a>前提条件

Visual Studio 2012 または Visual Studio 2010、Ultimate、Premium、Professional、または Web Express edition。

## <a name="create-the-database"></a>データベースを作成する

Visual Studio と共にインストールされるデータベースサーバーは、インストールされている Visual Studio のバージョンによって異なります。

-   Visual Studio 2012 を使用している場合は、LocalDB データベースを作成します。
-   Visual Studio 2010 を使用している場合は、SQL Express データベースを作成します。

まず、1つのエンティティに結合する2つのテーブルを含むデータベースを作成します。

-   Visual Studio を開く
-   **ビュー- &gt; サーバーエクスプローラー**
-   [**データ接続-接続の &gt; 追加**] を右クリックします。
-   サーバーエクスプローラーからデータベースに接続していない場合は、データソースとして **Microsoft SQL Server** を選択する必要があります。
-   インストールされているものに応じて、LocalDB または SQL Express に接続します。
-   データベース名として「 **EntitySplitting** 」と入力します。
-   [ **OK]** を選択すると、新しいデータベースを作成するかどうかを確認するメッセージが表示されます。 [**はい**] を選択します。
-   新しいデータベースがに表示されるようになりサーバーエクスプローラー
-   Visual Studio 2012 を使用している場合
    -   [サーバー エクスプローラー] でこのデータベースを右クリックし、**[新しいクエリ]** を選択します。
    -   次の SQL を新しいクエリにコピーし、クエリを右クリックして、[**実行**] を選択します。
-   Visual Studio 2010 を使用している場合
    -   [**データの選択]-[ &gt; transact-sql エディター]-[ &gt; 新しいクエリ接続**]
    -   「」と入力**します。 \\**サーバー名として SQLEXPRESS を指定し、[ **OK]** をクリックします。
    -   クエリエディターの上部にあるドロップダウンから **EntitySplitting** データベースを選択します。
    -   次の SQL を新しいクエリにコピーし、クエリを右クリックして、[ **sql の実行**] を選択します。

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
-   左側のウィンドウで、[ **Visual C \# **] をクリックし、[**コンソールアプリケーション**] テンプレートを選択します。
-   プロジェクトの名前として「 **Mapentityを** 入力し、[ **OK]** をクリックします。
-   最初のセクションで作成した SQL クエリの保存を確認するメッセージが表示されたら、[ **いいえ** ] をクリックします。

## <a name="create-a-model-based-on-the-database"></a>データベースに基づいてモデルを作成する

-   ソリューションエクスプローラーでプロジェクト名を右クリックして [ **追加**] をポイントし、[ **新しい項目**] をクリックします。
-   左側のメニューから [ **データ** ] を選択し、[テンプレート] ペインで [ **ADO.NET Entity Data Model** ] を選択します。
-   ファイル名として「 **MapEntityToTablesModel** 」と入力し、[ **追加**] をクリックします。
-   [モデルのコンテンツの選択] ダイアログボックスで、[ **データベースから生成**] を選択し、[次へ] をクリックし **ます。**
-   ドロップダウンから **EntitySplitting** 接続を選択し、[ **次へ**] をクリックします。
-   [データベースオブジェクトの選択] ダイアログボックスで、[**テーブル**] ノードの横にあるチェックボックスをオンにし   ます。
    これにより、 **EntitySplitting** データベースのすべてのテーブルがモデルに追加されます。
-   [ **完了**] をクリックします。

モデルを編集するためのデザイン画面を提供する Entity Designer が表示されます。

## <a name="map-an-entity-to-two-tables"></a>2つのテーブルへのエンティティのマップ

この手順で **は、person エンティティ型** を更新して、 **person** テーブルと個人 **情報** テーブルのデータを結合します。

-    **Email**    **個人情報 **エンティティの電子メールと**電話**のプロパティを選択し、ctrl キーを押し**ながら X**キーを押します。
-    **Person **エンティティを選択し、ctrl キーを押し**ながら V**キーを押します。
-   デザイン画面で、[ **個人情報**] エンティティを選択   し、キーボードの**del**キーを押します。
-   モデルから個人**情報**テーブルを削除するかどうかを確認するメッセージが表示されたら、[**いいえ**] をクリックします。これは**Person**エンティティにマップしようとしています。

    ![テーブルの削除](~/ef6/media/deletetables.png)

次の手順では、[ **マッピングの詳細**   ] ウィンドウが必要です。 このウィンドウが表示されない場合は、デザイン画面を右クリックし、[ **マッピングの詳細**] を選択します。

-    **Person**   エンティティ型を選択し、[マッピングの詳細] ウィンドウで [ ** &lt; テーブルまたは &gt; ビューの追加**] をクリックし    **Mapping Details**   ます。
-   ドロップダウンリストから [ **個人情報 **] を選択し   ます。
    [ **マッピングの詳細**   ] ウィンドウは、既定の列マッピングで更新されます。これは、このシナリオでは問題ありません。

 **Person**   エンティティ型は、person テーブルと個人 **Person**    **情報**テーブルにマップされるようになりました   。

![マッピング2](~/ef6/media/mapping2.png)

## <a name="use-the-model"></a>モデルを使用する

-   Main メソッドに次のコードを貼り付けます。

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

このアプリケーションを実行した結果、データベースに対して次の T-sql ステートメントが実行されました。 

-   次の2つの **INSERT** ステートメントは、コンテキストの実行結果として実行されました。SaveChanges ()。 **Person**エンティティからデータを取得し、 **person**テーブルと個人**情報**テーブルの間で分割します。

    ![挿入1](~/ef6/media/insert1.png)

    ![2の挿入](~/ef6/media/insert2.png)
-   次の **SELECT** は、データベース内の people を列挙した結果として実行されました。 **Person**テーブルと個人**情報**テーブルのデータを結合します。

    ![個人情報と個人情報データの組み合わせを選択する](~/ef6/media/select.png)
