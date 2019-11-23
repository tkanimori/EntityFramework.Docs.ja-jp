---
title: デザイナーのテーブル分割-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 452f17c3-9f26-4de4-9894-8bc036e23b0f
ms.openlocfilehash: f5e7532e6c0b473d8ce77cbd11e3e673b0af6cbe
ms.sourcegitcommit: b2b9468de2cf930687f8b85c3ce54ff8c449f644
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70921776"
---
# <a name="designer-table-splitting"></a>デザイナーのテーブル分割
このチュートリアルでは、Entity Framework Designer (EF Designer) を使用してモデルを変更することで、複数のエンティティ型を1つのテーブルにマップする方法について説明します。

テーブル分割を使用する理由の1つとして、遅延読み込みを使用してオブジェクトを読み込むときに、一部のプロパティの読み込みが遅れることがあります。 非常に大量のデータを含む可能性のあるプロパティを別のエンティティに分割し、必要な場合にのみ読み込むことができます。

次の図は、EF デザイナーを操作するときに使用されるメインウィンドウを示しています。

![EF デザイナー](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するための要件を次に示します。

- Visual Studio の最新バージョン。
- [School サンプルデータベース](~/ef6/resources/school-database.md)。

## <a name="set-up-the-project"></a>プロジェクトを設定する

このチュートリアルでは、Visual Studio 2012 を使用します。

-   Visual Studio 2012 を開きます。
-   **[ファイル]** メニューの **[新規作成]** をポイントし、 **[プロジェクト]** をクリックします。
-   左側のウィンドウで、[Visual C\#] をクリックし、[コンソールアプリケーション] テンプレートを選択します。
-   プロジェクトの名前として「[]」**と入力し**、[ **OK]** をクリックします。

## <a name="create-a-model-based-on-the-school-database"></a>School データベースに基づくモデルを作成する

-   ソリューションエクスプローラーでプロジェクト名を右クリックして **[追加]** をポイントし、 **[新しい項目]** をクリックします。
-   左側のメニューから **[データ]** を選択し、テンプレート ペインで  **[ADO.NET Entity Data Model]** を選択します。
-   ファイル名として「」**と入力し**、 **[追加]** をクリックします。
-   [モデルのコンテンツの選択] ダイアログボックスで、[ **データベースから生成**] を選択し、[次へ] をクリックし **ます。**
-   [新しい接続] をクリックします。 [接続プロパティ] ダイアログボックスで、サーバー名 (たとえば、 **(localdb)\\mssqllocaldb**) を入力し、認証方法を選択します。データベース名として「 **School** 」と入力し、[ **OK**] をクリックします。
    [データ接続の選択] ダイアログボックスが、データベース接続の設定によって更新されます。
-   [データベースオブジェクトの選択] ダイアログボックスで、[**テーブル** ] ノードを展開し、 **[Person]** テーブルを確認します。 これにより、指定したテーブルが**School**モデルに追加されます。
-   [ **完了**] をクリックします。

モデルを編集するためのデザイン画面を提供する Entity Designer が表示されます。 [ **データベースオブジェクトの選択** ] ダイアログボックスで選択したすべてのオブジェクトがモデルに追加されます。

## <a name="map-two-entities-to-a-single-table"></a>2つのエンティティを1つのテーブルにマップする

このセクションでは、 **Person**エンティティを2つのエンティティに分割し、1つのテーブルにマップします。

> [!NOTE]
> **Person**エンティティには、大量のデータを含む可能性のあるプロパティが含まれていません。例として使用されています。

-   デザイン画面の空の領域を右クリックし、[ **新規追加**] をポイントして、[ **エンティティ**] をクリックします。
    [ **新しいエンティティ** ] ダイアログボックスが表示されます。
-   [ **エンティティ名**] に「 **hireinfo** 」と入力し、**キープロパティ**名に「 **PersonID** 」と入力します。
-   [ **OK]** をクリックします。
-   新しいエンティティ型が作成され、デザイン画面に表示されます。
-    **Person** エンティティ型の **HireDate** プロパティを選択し、ctrl キーを押し**ながら X**キーを押します。
-   **Hireinfo** エンティティを選択し、ctrl キーを押し**ながら V**キーを押します。
-   **Person**と**hireinfo**の間の関連付けを作成します。 これを行うには、デザイン画面の空の領域を右クリックし、[ **新規追加**] をポイントして、[ **関連付け**] をクリックします。
-   [ **関連付け**の の追加] ダイアログボックスが表示されます。 既定では、**個人情報**の名前が指定されています。
-   リレーションシップの両方の end で多重度**1 (1)** を指定します。
-   **[OK]** をクリックします。

次の手順では、[ **マッピングの詳細** ] ウィンドウが必要です。 このウィンドウが表示されない場合は、デザイン画面を右クリックし、 **[マッピングの詳細]** を選択します。

-    **Hireinfo** エンティティ型を選択し、 **マッピングの詳細**  ウィンドウで  **テーブルまたはビューを追加**します&gt; &lt;クリックします。
-   [**テーブルまたはビュー&gt;**  フィールド] ボックス&lt;の一覧から **[個人]** を選択します。 この一覧には、選択したエンティティをマップできるテーブルまたはビューが含まれています。
    既定では、適切なプロパティをマップする必要があります。

    ![マッピング](~/ef6/media/mapping.png)

-   デザイン画面で、 **[個人情報]** の関連付けを選択します。
-   デザイン画面でアソシエーションを右クリックし、 **[プロパティ]** を選択します。
-   **[プロパティ]** ウィンドウで、 **[参照]** に関する制約 プロパティを選択し、省略記号ボタンをクリックします。
-   **[プリンシパル]** ドロップダウンリストから **[Person]** を選択します。
-   **[OK]** をクリックします。

 

## <a name="use-the-model"></a>モデルを使用する

-   Main メソッドに次のコードを貼り付けます。

``` csharp
    using (var context = new SchoolEntities())
    {
        Person person = new Person()
        {
            FirstName = "Kimberly",
            LastName = "Morgan",
            Discriminator = "Instructor",
        };

        person.HireInfo = new HireInfo()
        {
            HireDate = DateTime.Now
        };

        // Add the new person to the context.
        context.People.Add(person);

        // Insert a row into the Person table.  
        context.SaveChanges();

        // Execute a query against the Person table.
        // The query returns columns that map to the Person entity.
        var existingPerson = context.People.FirstOrDefault();

        // Execute a query against the Person table.
        // The query returns columns that map to the Instructor entity.
        var hireInfo = existingPerson.HireInfo;

        Console.WriteLine("{0} was hired on {1}",
            existingPerson.LastName, hireInfo.HireDate);
    }
```
-   アプリケーションをコンパイルして実行します。

このアプリケーションを実行した結果、 **School**データベースに対して次の t-sql ステートメントが実行されました。 

-   次の**INSERT**は、コンテキストの実行結果として実行されました。SaveChanges () と**Person**および**hireinfo**エンティティのデータを結合します。

    ![［挿入］](~/ef6/media/insert.png)

-   次の**SELECT**は、コンテキストの実行結果として実行されました。FirstOrDefault () を選択し、 **Person**にマップされた列のみを選択します

    ![1を選択します](~/ef6/media/select1.png)

-   次の**SELECT**は、ナビゲーションプロパティ existingperson にアクセスした結果として実行され、 **hireinfo**にマップされた列のみを選択しました。

    ![2を選択](~/ef6/media/select2.png)
