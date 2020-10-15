---
title: デザイナー TPT 継承-EF6
description: Entity Framework 6 でのデザイナーの継承
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/inheritance/tpt
ms.openlocfilehash: f8e4198278fb44309916d57b44fb9af41d572108
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066175"
---
# <a name="designer-tpt-inheritance"></a>デザイナーの TPT 継承
このステップバイステップチュートリアルでは、Entity Framework Designer (EF デザイナー) を使用してモデルにテーブル単位 (TPT) の継承を実装する方法を示します。 Table-Per-Type 継承は、データベース内の別個のテーブルを使用して、非継承プロパティと継承階層のそれぞれの型のキー プロパティのデータを維持します。

このチュートリアルでは、**コース**(基本型)、 **OnlineCourse** (course から派生)、および **OnsiteCourse**   ( **course**から派生) エンティティを同じ名前のテーブルにマップします。 データベースからモデルを作成し、TPT 継承を実装するようにモデルを変更します。

Model First から開始してから、モデルからデータベースを生成することもできます。 EF デザイナーでは、既定で TPT 戦略が使用されるため、モデル内のすべての継承が個別のテーブルにマップされます。

## <a name="other-inheritance-options"></a>その他の継承オプション

階層ごとのテーブル (TPH) は、継承階層内のすべてのエンティティ型のデータを保持するために1つのデータベーステーブルが使用されるもう1つの種類の継承です。Entity Designer によるテーブルごとの継承のマッピング方法の詳細については、「 [EF DESIGNER TPH の継承](xref:ef6/modeling/designer/inheritance/tph)」を参照してください。 

具象型継承 (TPC) と混合継承モデルは、Entity Framework ランタイムによってサポートされていますが、EF デザイナーではサポートされていないことに注意してください。 TPC または混合継承を使用する場合は、Code First を使用する方法と、EDMX ファイルを手動で編集する方法の2つのオプションがあります。 EDMX ファイルを使用することを選択した場合、[マッピングの詳細] ウィンドウは "セーフモード" になり、デザイナーを使用してマッピングを変更することはできません。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するための要件を次に示します。

- Visual Studio の最新バージョン。
- [School サンプルデータベース](xref:ef6/resources/school-database)。

## <a name="set-up-the-project"></a>プロジェクトを設定する

-   Visual Studio 2012 を開きます。
-   **ファイルの選択- &gt; 新規作成- &gt; プロジェクト**
-   左側のウィンドウで、[ **Visual C \# **] をクリックし、**コンソール**テンプレートを選択します。
-   名前として「 **Tptdbfirstsample**」と入力し   ます。
-    **[OK]** を選択します。

## <a name="create-a-model"></a>モデルの作成

-   ソリューションエクスプローラーでプロジェクトを右クリックし、[追加]、[ ** &gt; 新しい項目**] の順に選択します。
-   左側のメニューから [ **データ** ] を選択し、[テンプレート] ペインで [ **ADO.NET Entity Data Model** ] を選択します。
-   ファイル名として「 **Tptmodel. .edmx** 」と入力し、[ **追加**] をクリックします。
-   [モデルのコンテンツの選択] ダイアログボックスで、[**   データベースから生成**] を選択し、[ **次へ**] をクリックします。
-   [ **新しい接続**] をクリックします。
    [接続プロパティ] ダイアログボックスで、サーバー名 (たとえば、 **(localdb) \\ mssqllocaldb**) を入力し、認証方法を選択します。データベース名として「 **School**」と入力し、[    **OK]** をクリックします。
    [データ接続の選択] ダイアログボックスが、データベース接続の設定によって更新されます。
-   [データベースオブジェクトの選択] ダイアログボックスの [テーブル] ノードで、 **Department**、 **Course、OnlineCourse、および OnsiteCourse** の各テーブルを選択します。
-   [ **完了**] をクリックします。

モデルを編集するためのデザイン画面を提供する Entity Designer が表示されます。 [データベースオブジェクトの選択] ダイアログボックスで選択したすべてのオブジェクトがモデルに追加されます。

## <a name="implement-table-per-type-inheritance"></a>型ごとのテーブル継承の実装

-   デザイン画面で、 **OnlineCourse** エンティティ型を右クリックし、[ **プロパティ**] を選択します。
-   [ **プロパティ** ] ウィンドウで、[基本データ型] プロパティを「 **Course**」に設定します。
-   **OnsiteCourse**エンティティ型を右クリックし、[**プロパティ**] を選択します。
-   [ **プロパティ** ] ウィンドウで、[基本データ型] プロパティを「 **Course**」に設定します。
-   **OnlineCourse**エンティティ型と**Course**エンティティ型の間のアソシエーション (線) を右クリックします。
    [ **モデルから削除**] を選択します。
-   **OnsiteCourse**エンティティ型と**Course**エンティティ型の間のアソシエーションを右クリックします。
    [ **モデルから削除**] を選択します。

ここでは、 **OnlineCourse**と**OnsiteCourse**から**CourseID**プロパティを削除します。これらのクラスは、 **Course**基本型から**CourseID**を継承するためです。

-   **OnlineCourse**エンティティ型の**CourseID**プロパティを右クリックし、[**モデルから削除**] を選択します。
-   **OnsiteCourse**エンティティ型の**CourseID**プロパティを右クリックし、[**モデルから削除**] を選択します。
-   これで、Table-Per-Type 継承が実装されました。

![種類ごとのテーブル](~/ef6/media/tpt.png)

## <a name="use-the-model"></a>モデルを使用する

**Main**メソッドが定義されている**Program.cs**ファイルを開きます。 **Main**関数に次のコードを貼り付けます。 このコードは、3つのクエリを実行します。 最初のクエリでは、指定された部門に関連するすべての **コース** が返されます。 2番目のクエリでは、 **OfType** メソッドを使用して、指定された department に関連する **OnlineCourses** を返します。 3番目のクエリは、 **OnsiteCourses**を返します。

``` csharp
    using (var context = new SchoolEntities())
    {
        foreach (var department in context.Departments)
        {
            Console.WriteLine("The {0} department has the following courses:",
                               department.Name);

            Console.WriteLine("   All courses");
            foreach (var course in department.Courses )
            {
                Console.WriteLine("     {0}", course.Title);
            }

            foreach (var course in department.Courses.
                OfType<OnlineCourse>())
            {
                Console.WriteLine("   Online - {0}", course.Title);
            }

            foreach (var course in department.Courses.
                OfType<OnsiteCourse>())
            {
                Console.WriteLine("   Onsite - {0}", course.Title);
            }
        }
    }
```
