---
title: Table-Valued 関数 (Tvf)-EF6
description: Entity Framework 6 の Table-Valued 関数 (Tvf)
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/tvfs
ms.openlocfilehash: 2c699cad3ace253842d17aca4eca4df2318c4795
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066201"
---
# <a name="table-valued-functions-tvfs"></a>Table-Valued 関数 (Tvf)
> [!NOTE]
> **EF5** 以降: このページで説明した機能、api などは、Entity Framework 5 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。

ビデオとステップバイステップのチュートリアルでは、Entity Framework Designer を使用してテーブル値関数 (Tvf) をマップする方法について説明します。 また、LINQ クエリから TVF を呼び出す方法についても説明します。

Tvf は現在、Database First ワークフローでのみサポートされています。

TVF サポートは Entity Framework version 5 で導入されました。 テーブル値関数、列挙型、空間型などの新機能を使用するには、.NET Framework 4.5 をターゲットにする必要があります。 既定では、Visual Studio 2012 は .NET 4.5 を対象としています。

Tvf はストアドプロシージャとよく似ていますが、1つの重要な違いがあります。 TVF の結果は、コンポーザブルです。 つまり、TVF の結果は LINQ クエリで使用できますが、ストアドプロシージャの結果は使用できません。

## <a name="watch-the-video"></a>ビデオを見る

**表示者**: ジュリアつい

[WMV](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.wmv)  | [MP4](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-mp4video-tvf.m4v)  | [WMV (ZIP)](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.zip)

## <a name="pre-requisites"></a>前提条件

このチュートリアルを完了するには、次の手順を実行する必要があります。

- [School データベース](xref:ef6/resources/school-database)をインストールします。

- Visual Studio の最新バージョンがあること

## <a name="set-up-the-project"></a>プロジェクトを設定する

1.  Visual Studio を開く
2.  [**ファイル**] メニューの [**新規作成**] をポイントし、[**プロジェクト**] をクリックします。
3.  左側のウィンドウで、[ **Visual C \# **] をクリックし、**コンソール**テンプレートを選択します。
4.  プロジェクトの名前として「 **TVF** 」と入力し、[ **OK]** をクリックします。

## <a name="add-a-tvf-to-the-database"></a>TVF をデータベースに追加する

-   **ビュー &gt; SQL Server オブジェクトエクスプローラーの**選択
-   LocalDB がサーバーの一覧にない場合: **SQL Server** を右クリックし、[追加] を選択 **SQL Server** 既定の **Windows 認証** を使用して localdb サーバーに接続する
-   LocalDB ノードを展開します。
-   [データベース] ノードで、School データベースノードを右クリックし、[ **新しいクエリ**] を選択します。
-   T-sql エディターで、次の TVF 定義を貼り付けます。

``` SQL
CREATE FUNCTION [dbo].[GetStudentGradesForCourse]

(@CourseID INT)

RETURNS TABLE

RETURN
    SELECT [EnrollmentID],
           [CourseID],
           [StudentID],
           [Grade]
    FROM   [dbo].[StudentGrade]
    WHERE  CourseID = @CourseID
```

-   T-sql エディターでマウスの右ボタンをクリックし、[ **実行**] を選択します。
-   GetStudentGradesForCourse 関数が School データベースに追加されます。

 

## <a name="create-a-model"></a>モデルの作成

1.  ソリューションエクスプローラーでプロジェクト名を右クリックし、[**追加**] をポイントして、[**新しい項目**] をクリックします。
2.  左側のメニューから [**データ**] を選択し、[**テンプレート**] ペインで [ **ADO.NET Entity Data Model** ] を選択します。
3.  ファイル名として「TVFModel」と入力し、[**追加**] をクリックし**ます。**
4.  [モデルのコンテンツの選択] ダイアログボックスで、[ **データベースから生成**] を選択し、[ **次へ**] をクリックします。
5.  [ **新しい接続**] をクリックします。 [サーバー名] ボックスに**School**「 ** \\ mssqllocaldb** 」と入力し、   データベース名として「School」と入力します。 [ **OK]** をクリックします。
6.  [データベースオブジェクトの選択] ダイアログボックスの [ **テーブル**] ノードで、    **Person**、 **StudentGrade**、および **Course**の各テーブルを選択します。  
7.  [ **ストアドプロシージャと関数**] ノードの下にある **GetStudentGradesForCourse**関数を選択します。これは、   Visual Studio Entity Designer 2012 以降では、ストアドプロシージャと関数をバッチインポートすることができます。
8.  [ **完了**] をクリック
9.  モデルを編集するためのデザイン画面を提供する Entity Designer が表示されます。 [ **データベースオブジェクトの選択**] ダイアログボックスで選択したすべてのオブジェクト   がモデルに追加されます。
10. 既定では、インポートされた各ストアドプロシージャまたは関数の結果構造は、自動的にエンティティモデルの新しい複合型になります。 ただし、GetStudentGradesForCourse 関数の結果を StudentGrade エンティティにマップする必要があります。デザイン画面を右クリックし、モデルブラウザーで [ **モデルブラウザー** ] を選択し、[ **関数**インポート] を選択します。次に、[関数インポートの編集] ダイアログボックスで **GetStudentGradesForCourse**関数をダブルクリックし、[ **エンティティ**] を選択し   て [ **StudentGrade** ] を選択します。

## <a name="persist-and-retrieve-data"></a>データの永続化と取得

Main メソッドが定義されているファイルを開きます。 Main 関数に次のコードを追加します。

次のコードは、テーブル値関数を使用するクエリを作成する方法を示しています。 このクエリでは、関連するコースのタイトルと、3.5 以上のレベルを持つ関連する学生を含む匿名型に結果を射影します。

``` csharp
using (var context = new SchoolEntities())
{
    var CourseID = 4022;
    var Grade = 3.5M;

    // Return all the best students in the Microeconomics class.
    var students = from s in context.GetStudentGradesForCourse(CourseID)
                            where s.Grade >= Grade
                            select new
                            {
                                s.Person,
                                s.Course.Title
                            };

    foreach (var result in students)
    {
        Console.WriteLine(
            "Couse: {0}, Student: {1} {2}",
            result.Title,  
            result.Person.FirstName,  
            result.Person.LastName);
    }
}
```

アプリケーションをコンパイルして実行します。 このプログラムの出力は、次のようになります。

```console
Couse: Microeconomics, Student: Arturo Anand
Couse: Microeconomics, Student: Carson Bryant
```

## <a name="summary"></a>まとめ

このチュートリアルでは、Entity Framework Designer を使用してテーブル値関数 (Tvf) をマップする方法について説明しました。 また、LINQ クエリから TVF を呼び出す方法についても説明します。
