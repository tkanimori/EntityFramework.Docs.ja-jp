---
title: デザイナーのクエリのストアド プロシージャ - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9554ed25-c5c1-43be-acad-5da37739697f
ms.openlocfilehash: 04478ea1c8cd43a7ba4ee788e464992af3de7f64
ms.sourcegitcommit: 269c8a1a457a9ad27b4026c22c4b1a76991fb360
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46283902"
---
# <a name="designer-query-stored-procedures"></a>デザイナーのクエリのストアド プロシージャ
このステップ バイ ステップ チュートリアルでは、モデルにストアド プロシージャをインポートする Entity Framework デザイナー (EF Designer) を使用し、結果を取得する場合は、インポートされたストアド プロシージャを呼び出す方法を説明します。 

Code First はサポートされていないことをストアド プロシージャまたは関数のマッピングに注意してください。 ただし、System.Data.Entity.DbSet.SqlQuery メソッドを使用して、ストアド プロシージャまたは関数を呼び出すことができます。 例えば:
``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]")`;
```

## <a name="prerequisites"></a>必須コンポーネント

このチュートリアルを完了するための要件を次に示します。

- Visual Studio の最新バージョン。
- [School サンプル データベース](~/ef6/resources/school-database.md)します。

## <a name="set-up-the-project"></a>プロジェクトを設定します。

-   Visual Studio 2012 を開きます。
-   選択**ファイル -&gt;新機能 -&gt;プロジェクト**
-   左側のウィンドウで次のようにクリックします。 **Visual C\#** を選び、**コンソール**テンプレート。
-   入力**EFwithSProcsSample**名として。
-   **[OK]** を選択します。

## <a name="create-a-model"></a>モデルを作成します。

-   ソリューション エクスプ ローラーでプロジェクトを右クリックして**追加 -&gt;新しい項目の**します。
-   選択**データ**選択し、左側のメニューから**ADO.NET Entity Data Model**テンプレート ペインでします。
-   入力**EFwithSProcsModel.edmx**のファイル名、およびクリック**追加**します。
-   モデルのコンテンツの選択 ダイアログ ボックスで、次のように選択します。**データベースから生成**、 をクリックし、**次**。
-   クリックして**新しい接続**します。  
    接続のプロパティ ダイアログ ボックスで、サーバー名を入力します (たとえば、 **(localdb)\\mssqllocaldb**) を選択します、認証方式として、型**学校**データベース名、し、。クリックして**OK**します。  
    データ接続の選択 ダイアログ ボックスは、データベース接続の設定で更新されます。
-   データベース オブジェクトの選択 ダイアログ ボックスで、確認、**テーブル** チェック ボックスをすべてのテーブルを選択します。  
    また、次のストアド プロシージャの選択、**ストアド プロシージャおよび関数**ノード: **GetStudentGrades**と**GetDepartmentName**します。 

    ![インポート](~/ef6/media/import.jpg)

    *Visual Studio 2012 で EF Designer を起動すると、ストアド プロシージャの一括インポートがサポートしています。**エンティ モデルに選択されたストアド プロシージャおよび関数のインポート**が既定でオンになっています。*
-   **[完了]** をクリックします。

既定では、各インポートされたストアド プロシージャまたは複数の列を返す関数の結果形状の新しい複合型に自動的になります。 結果をマップするこの例では、 **GetStudentGrades**関数を**StudentGrade**エンティティとの結果、 **GetDepartmentName** に**none** (**none**は既定値です)。

エンティティ型を返す関数インポート、対応するストアド プロシージャによって返される列は、返されるエンティティ型のスカラー プロパティを正確に一致する必要があります。 関数インポートでは、単純型、複合型、または値のコレクションを返すこともできます。

-   デザイン サーフェスと選択を右クリックして**モデル ブラウザー**します。
-   **モデル ブラウザー**を選択します**関数インポート**、し、ダブルクリック、 **GetStudentGrades**関数。
-   関数インポートの編集] ダイアログ ボックスで、[**エンティティ**選択**StudentGrade**します。  
    ***関数インポートがコンポーザブル**の上部にあるチェック ボックスをオン、**関数インポート**ダイアログには、構成可能な関数にマップすることができます。このボックスをオンにしないでくださいコンポーザブルな関数 (テーブル値関数) のみがで表示されます、**ストアド プロシージャ/関数名**ドロップダウン リスト。このボックスをオンにしない場合、非コンポーザブル関数のみが一覧に表示されます。*

## <a name="use-the-model"></a>モデルを使用します。

開く、 **Program.cs**ファイルの場所、 **Main**メソッドを定義します。 Main 関数には、次のコードを追加します。

コードは、2 つのストアド プロシージャを呼び出します: **GetStudentGrades** (返します**StudentGrades** 、指定された*StudentId*) と**GetDepartmentName**(出力パラメーターで部署の名前を返します。)  

``` csharp
    using (SchoolEntities context = new SchoolEntities())
    {
        // Specify the Student ID.
        int studentId = 2;

        // Call GetStudentGrades and iterate through the returned collection.
        foreach (StudentGrade grade in context.GetStudentGrades(studentId))
        {
            Console.WriteLine("StudentID: {0}\tSubject={1}", studentId, grade.Subject);
            Console.WriteLine("Student grade: " + grade.Grade);
        }

        // Call GetDepartmentName.
        // Declare the name variable that will contain the value returned by the output parameter.
        ObjectParameter name = new ObjectParameter("Name", typeof(String));
        context.GetDepartmentName(1, name);
        Console.WriteLine("The department name is {0}", name.Value);

    }
```

アプリケーションをコンパイルして実行します。 このプログラムの出力は、次のようになります。

```
StudentID: 2
Student grade: 4.00
StudentID: 2
Student grade: 3.50
The department name is Engineering
```

<a name="output-parameters"></a>出力パラメーター
-----------------

出力パラメーターを使用している場合は、結果が完全に読み取られるまでに、その値は使用できません。 これは、DbDataReader の基になる動作によるものを参照してください[DataReader によるデータの取得](https://go.microsoft.com/fwlink/?LinkID=398589)の詳細。
