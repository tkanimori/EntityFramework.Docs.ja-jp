---
title: デザイナークエリストアドプロシージャ-EF6
description: Entity Framework 6 のデザイナークエリストアドプロシージャ
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/stored-procedures/query
ms.openlocfilehash: 4976c7d4f788e6e9524055a508f9c906b47c9ebb
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073202"
---
# <a name="designer-query-stored-procedures"></a>デザイナークエリのストアドプロシージャ
このステップバイステップチュートリアルでは、Entity Framework Designer (EF デザイナー) を使用してストアドプロシージャをモデルにインポートし、インポートされたストアドプロシージャを呼び出して結果を取得する方法について説明します。 

この Code First では、ストアドプロシージャまたは関数へのマッピングはサポートされていません。 ただし、ストアドプロシージャまたは関数は、ストアドプロシージャまたは関数を呼び出すことによって呼び出すことができます。 次に例を示します。
``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]")`;
```

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するための要件を次に示します。

- Visual Studio の最新バージョン。
- [School サンプルデータベース](xref:ef6/resources/school-database)。

## <a name="set-up-the-project"></a>プロジェクトを設定する

-   Visual Studio 2012 を開きます。
-   **ファイルの選択- &gt; 新規作成- &gt; プロジェクト**
-   左側のウィンドウで、[ **Visual C \# **] をクリックし、**コンソール**テンプレートを選択します。
-   名前として「 **Efwithsprocssample**」と入力し   ます。
-    **[OK]** を選択します。

## <a name="create-a-model"></a>モデルの作成

-   ソリューションエクスプローラーでプロジェクトを右クリックし、[追加]、[ ** &gt; 新しい項目**] の順に選択します。
-   左側のメニューから [ **データ** ] を選択し、[テンプレート] ペインで [ **ADO.NET Entity Data Model** ] を選択します。
-   ファイル名として「 **Efwithsprocsmodel. .edmx** 」と入力し、[ **追加**] をクリックします。
-   [モデルのコンテンツの選択] ダイアログボックスで、[ **データベースから生成**] を選択し、[ **次へ**] をクリックします。
-   [ **新しい接続**] をクリックします。  
    [接続プロパティ] ダイアログボックスで、サーバー名 (たとえば、 **(localdb) \\ mssqllocaldb**) を入力し、認証方法を選択します。データベース名として「 **School**」と入力し、[    **OK]** をクリックします。  
    [データ接続の選択] ダイアログボックスが、データベース接続の設定によって更新されます。
-   [データベースオブジェクトの選択] ダイアログボックスで、[**テーブル**] チェックボックスをオンにして   、すべてのテーブルを選択します。  
    また、[ **ストアドプロシージャと関数** ] ノードで、 **GetStudentGrades** および **GetDepartmentName**の下にある次のストアドプロシージャを選択します。 

    ![ストアドプロシージャのインポート](~/ef6/media/import.jpg)

    *Visual Studio 2012 以降では、EF デザイナーはストアドプロシージャの一括インポートをサポートしています。既定では、[ **選択したストアドプロシージャと関数をエンティティモデルにインポート** する] がオンになっています。*
-   [ **完了**] をクリックします。

既定では、インポートされた各ストアドプロシージャまたは複数の列を返す関数の結果構造は、自動的に新しい複合型になります。 この例では、 **GetStudentGrades** 関数の結果を **StudentGrade** エンティティにマップし、 **GetDepartmentName** の結果を **none** (既定値は**none** ) にマップします。

関数インポートでエンティティ型を返すには、対応するストアドプロシージャによって返される列が、返されたエンティティ型のスカラープロパティと正確に一致している必要があります。 関数インポートは、単純型のコレクション、複合型、または値を返さないこともできます。

-   デザイン画面を右クリックし、[ **モデルブラウザー**] を選択します。
-   **モデルブラウザー**で、[ **関数インポート**] を選択し、 **GetStudentGrades**関数をダブルクリックします。
-   [関数インポートの編集] ダイアログボックスで、[ **エンティティ**] を選択し、[StudentGrade] を   選択します。 **StudentGrade**  
    *[**関数**インポート] ダイアログボックスの上部にある [**関数インポートはコンポーザブル**] チェックボックスをオンにすると、コンポーザブルな関数にマップできます。このチェックボックスをオンにすると、[**ストアドプロシージャ/関数名**] ボックスの一覧に、コンポーザブルな関数 (テーブル値関数) のみが表示されます。このチェックボックスをオンにしない場合、一覧には、コンポーザブルではない関数のみが表示されます。*

## <a name="use-the-model"></a>モデルを使用する

**Main**メソッドが定義されている**Program.cs**ファイルを開きます。 Main 関数に次のコードを追加します。

このコードは2つのストアドプロシージャを呼び出します。 **GetStudentGrades** (指定された*StudentId*の**StudentGrades**を返します) と**GetDepartmentName** (出力パラメーターで department の名前を返します) を呼び出します。  

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

```console
StudentID: 2
Student grade: 4.00
StudentID: 2
Student grade: 3.50
The department name is Engineering
```

<a name="output-parameters"></a>出力パラメーター
-----------------

出力パラメーターが使用されている場合、結果が完全に読み取られるまで、値は使用できません。 これは、DbDataReader の基になる動作に起因します。詳細については、「 [DataReader を使用したデータの取得](https://go.microsoft.com/fwlink/?LinkID=398589) 」を参照してください。
