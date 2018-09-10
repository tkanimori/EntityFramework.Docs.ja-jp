---
title: デザイナーのテーブルは、次の分割の EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 452f17c3-9f26-4de4-9894-8bc036e23b0f
ms.openlocfilehash: f07aeb0aa679f6fa8131c667ac808f17c3f03f20
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2018
ms.locfileid: "44250986"
---
# <a name="designer-table-splitting"></a>デザイナーのテーブルを分割
このチュートリアルでは、Entity Framework デザイナー (EF Designer) を持つモデルを変更することで、複数のエンティティ型を 1 つのテーブルにマップする方法を示します。

テーブル分割を使用する理由の 1 つは、遅延読み込み、オブジェクトの読み込みに使用する場合、一部のプロパティの読み込みを遅らせることが。 個別のエンティティに非常に大量のデータを含めることが必要な場合のみを読み込む可能性のあるプロパティを分離することができます。

次の図は、EF Designer を使用する場合に使用される主なウィンドウを示します。

![EF デザイナー](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a>必須コンポーネント

このチュートリアルを完了するための要件を次に示します。

- Visual Studio の最新バージョン。
- [School サンプル データベース](~/ef6/resources/school-database.md)します。

## <a name="set-up-the-project"></a>プロジェクトを設定します。

このチュートリアルでは、Visual Studio 2012 を使用しています。

-   Visual Studio 2012 を開きます。
-   **[ファイル]** メニューの **[新規作成]** をポイントし、 **[プロジェクト]** をクリックします。
-   左側のウィンドウで、Visual C をクリックします。\#、し、コンソール アプリケーション テンプレートを選択します。
-   入力**TableSplittingSample**をクリックして、プロジェクトの名前として**OK**します。

## <a name="create-a-model-based-on-the-school-database"></a>School データベースに基づくモデルを作成します。

-   ソリューション エクスプ ローラーでプロジェクト名を右クリックし、[**追加**、] をクリックし、**新しい項目の**します。
-   選択**データ**選択し、左側のメニューから**ADO.NET Entity Data Model**テンプレート ペインでします。
-   入力**TableSplittingModel.edmx**のファイル名、およびクリック**追加**します。
-   モデルのコンテンツの選択 ダイアログ ボックスで、次のように選択します。**データベースから生成**、 をクリックし、 **次へ。**
-   [新しい接続] をクリックします。 接続のプロパティ ダイアログ ボックスで、サーバー名を入力します (たとえば、 **(localdb)\\mssqllocaldb**) を選択します、認証方式として、型**学校**データベース名、し、。クリックして**OK**します。
    データ接続の選択 ダイアログ ボックスは、データベース接続の設定で更新されます。
-   データベース オブジェクトの選択 ダイアログ ボックスで、展開、**テーブル**ノードとチェック、 **Person**テーブル。 これを指定したテーブルに追加されます、**学校**モデル。
-   **[完了]** をクリックします。

モデルを編集するため、デザイン サーフェイスを提供するエンティティ デザイナーが表示されます。 選択したすべてのオブジェクト、**データベース オブジェクトの選択** ダイアログ ボックスは、モデルに追加されます。

## <a name="map-two-entities-to-a-single-table"></a>2 つのエンティティを 1 つのテーブルにマップします。

このセクションに分割する、 **Person** 2 つのエンティティにエンティティし、それらを 1 つのテーブルにマップします。

> [!NOTE]
> **Person**エンティティに大量のデータを含む可能性のある任意のプロパティが含まれていません。 例としてだけに使用されます。

-   デザイン サーフェイスの空の領域を右クリックし、[**新規追加**、] をクリック**エンティティ**します。
    **新しいエンティティ** ダイアログ ボックスが表示されます。
-   型**HireInfo**の**エンティティ名**と**PersonID**の**キー プロパティ**名。
-   **[OK]** をクリックします。
-   新しいエンティティ型が作成され、デザイン画面に表示されます。
-   選択、 **HireDate**のプロパティ、 **Person**エンティティ型とキーを押して**Ctrl + X**キー。
-   選択、 **HireInfo**エンティティ キーを押します**Ctrl + V**キー。
-   間のアソシエーションを作成する**Person**と**HireInfo**します。 これを行うには、デザイン サーフェイスの空の領域を右クリックし、 をポイント**新規追加**、 をクリック**アソシエーション**します。
-   **の関連付けの追加** ダイアログ ボックスが表示されます。 **PersonHireInfo**既定で名前を指定します。
-   多重度を指定**1(One)** リレーションシップの両端でします。
-   **[OK]** を押します。

次の手順が必要です、**マッピングの詳細**ウィンドウ。 このウィンドウを表示されない場合は、サーフェスと選択のデザインを右クリックして**マッピングの詳細**します。

-   選択、 **HireInfo**エンティティの種類をクリックします**&lt;テーブルまたはビューの追加&gt;** で、**マッピングの詳細**ウィンドウ。
-   選択**Person**から、 **&lt;テーブルまたはビューの追加&gt;** フィールドのドロップダウン リスト。 リストは、テーブルが含まれていたり、ビューを選択したエンティティをマップできます。
    既定では、適切なプロパティをマップする必要があります。

    ![マップ](~/ef6/media/mapping.png)

-   選択、 **PersonHireInfo**デザイン画面でアソシエーション。
-   デザイン サーフェスと選択のアソシエーションを右クリックして**プロパティ**します。
-   **プロパティ**ウィンドウで、**参照に関する制約**プロパティの省略記号ボタンをクリックします。
-   選択**Person**から、**プリンシパル**ドロップダウン リスト。
-   **[OK]** を押します。

 

## <a name="use-the-model"></a>モデルを使用します。

-   Main メソッドでは、次のコードを貼り付けます。

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

に対して次の T-SQL ステートメントが実行された、**学校**このアプリケーションを実行した結果のデータベース。 

-   次**挿入**コンテキストを実行した結果として実行されました。データを SaveChanges() と組み合わせて、 **Person**と**HireInfo**エンティティ

    ![挿入](~/ef6/media/insert.png)

-   次**選択**コンテキストを実行した結果として実行されました。マップされている列だけ選択して People.FirstOrDefault()**人**

    ![1 を選択します](~/ef6/media/select1.png)

-   次**選択**ナビゲーション プロパティの existingPerson.Instructor へのアクセスの結果は実行しにマップされている列だけを選択します**HireInfo。**

    ![2 を選択します。](~/ef6/media/select2.png)
