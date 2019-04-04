---
title: デザイナー TPT 継承 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: efc78c31-b4ea-4ea3-a0cd-c69eb507020e
ms.openlocfilehash: 84330fba4807620aa242a70cd8ac76a60284416d
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45489454"
---
# <a name="designer-tpt-inheritance"></a>デザイナー TPT 継承
このステップ バイ ステップ チュートリアルでは、Entity Framework デザイナー (EF Designer) を使用して、モデル内のテーブル、単位の種類 (TPT) 継承を実装する方法を示します。 Table-Per-Type 継承は、データベース内の別個のテーブルを使用して、非継承プロパティと継承階層のそれぞれの型のキー プロパティのデータを維持します。

このチュートリアルでマップには、**コース**(基本データ型) **OnlineCourse** (コースから派生)、および**OnsiteCourse** (から派生**コース**)同じ名前を持つテーブルにエンティティ。 データベースからモデルを作成し、TPT 継承を実装するモデルを変更します。

Model First でも起動し、モデルからデータベースを生成できます。 EF Designer では、既定で、TPT 戦略を使用して、モデルでは、何も継承が別個のテーブルにマップするためです。

## <a name="other-inheritance-options"></a>その他の継承オプション

Table-per-hierarchy (TPH) では、継承、継承階層内のエンティティ型のすべてのデータを維持するためにテーブルを使用する 1 つのデータベース内の別の型です。  エンティティ デザイナーでの Table-per-hierarchy 継承をマップする方法については、[EF デザイナー TPH 継承](~/ef6/modeling/designer/inheritance/tph.md)を参照してください。 

具象ごとのテーブル型 (TPC) 継承と混在の継承モデルは、Entity Framework ランタイムによってサポートされますが、EF Designer でサポートされていないに注意してください。 2 つのオプションがある TPC または混在の継承を使用する場合は、: Code First を使用して、または EDMX ファイルを手動で編集します。 EDMX ファイルを使用する場合は、マッピングの詳細 ウィンドウは、「セーフ モード」に格納されます。 そして、デザイナーを使用して、マッピング変更することはできません。

## <a name="prerequisites"></a>必須コンポーネント

このチュートリアルを完了するための要件を次に示します。

- Visual Studio の最新バージョン。
- [School サンプル データベース](~/ef6/resources/school-database.md)します。

## <a name="set-up-the-project"></a>プロジェクトを設定します。

-   Visual Studio 2012 を開きます。
-   選択**ファイル -&gt;新機能 -&gt;プロジェクト**
-   左側のウィンドウで次のようにクリックします。 **Visual C\#** を選び、**コンソール**テンプレート。
-   入力**TPTDBFirstSample**名として。
-   **[OK]** を選択します。

## <a name="create-a-model"></a>モデルを作成します。

-   ソリューション エクスプ ローラーでプロジェクトを右クリックして**追加 -&gt;新しい項目の**します。
-   選択**データ**選択し、左側のメニューから**ADO.NET Entity Data Model**テンプレート ペインでします。
-   入力**TPTModel.edmx**のファイル名、およびクリック**追加**します。
-   モデルのコンテンツの選択 ダイアログ ボックスを選択します * * *、データベースから生成しをクリックして**次**します。
-   クリックして**新しい接続**します。
    接続のプロパティ ダイアログ ボックスで、サーバー名を入力します (たとえば、 **(localdb)\\mssqllocaldb**) を選択します、認証方式として、型**学校**データベース名、し、。クリックして**OK**します。
    データ接続の選択 ダイアログ ボックスは、データベース接続の設定で更新されます。
-   データベース オブジェクトの選択 ダイアログ ボックスの テーブル ノードを選択、**部門**、**コース、OnlineCourse、および OnsiteCourse**テーブル。
-   **[完了]** をクリックします。

モデルを編集するため、デザイン サーフェイスを提供するエンティティ デザイナーが表示されます。 [データベース オブジェクトの選択] ダイアログ ボックスで選択したすべてのオブジェクトは、モデルに追加されます。

## <a name="implement-table-per-type-inheritance"></a>テーブルの型ごとの継承を実装します。

-   デザイン サーフェイスを右クリックし、 **OnlineCourse**エンティティの種類と選択**プロパティ**します。
-   **プロパティ**ウィンドウ、基本データ型プロパティを設定**コース**します。
-   右クリックし、 **OnsiteCourse**エンティティの種類と選択**プロパティ**します。
-   **プロパティ**ウィンドウ、基本データ型プロパティを設定**コース**します。
-   間のアソシエーション (線) を右クリックし、 **OnlineCourse**と**コース**エンティティ型。
    選択**モデルから削除**します。
-   間の関連付けを右クリックし、 **OnsiteCourse**と**コース**エンティティ型。
    選択**モデルから削除**します。

私たちが削除されます、 **CourseID**プロパティから**OnlineCourse**と**OnsiteCourse**これらのクラスを継承するので、 **CourseID**から**コース**基本データ型。

-   右クリックし、 **CourseID**のプロパティ、 **OnlineCourse**エンティティ型、および選択**モデルから削除**します。
-   右クリックし、 **CourseID**のプロパティ、 **OnsiteCourse**エンティティ型、および選択**モデルから削除**
-   これで、Table-Per-Type 継承が実装されました。

![TPT](~/ef6/media/tpt.png)

## <a name="use-the-model"></a>モデルを使用します。

開く、 **Program.cs**ファイルの場所、 **Main**メソッドを定義します。 次のコードを貼り付け、 **Main**関数。 コードでは、次の 3 つのクエリを実行します。 最初のクエリを取り戻すすべて**コース**指定の部署に関連します。 2 番目のクエリを使用して、 **OfType**を返すメソッドを**OnlineCourses**指定の部署に関連します。 3 番目のクエリが返す**OnsiteCourses**します。

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
