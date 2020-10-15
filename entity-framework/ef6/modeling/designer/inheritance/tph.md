---
title: デザイナー TPH 継承-EF6
description: Entity Framework 6 でのデザイナーの継承
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/inheritance/tph
ms.openlocfilehash: bf689e02a76e46e5eee5a64cb9825e667c8cd65c
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064849"
---
# <a name="designer-tph-inheritance"></a>デザイナーの TPH 継承
このステップバイステップチュートリアルでは、Entity Framework Designer (EF デザイナー) を使用して概念モデルに階層構造 (TPH) の継承を実装する方法について説明します。 TPH 継承では、1つのデータベーステーブルを使用して、継承階層内のすべてのエンティティ型のデータを保持します。

このチュートリアルでは、person テーブルを、Person (基本型)、Student (Person から派生)、およびインストラクター (Person から派生) の3つのエンティティ型にマップします。 データベースから概念モデルを作成し (Database First)、EF デザイナーを使用して TPH 継承を実装するようにモデルを変更します。

Model First を使用して TPH 継承にマップすることはできますが、複雑な独自のデータベース生成ワークフローを作成する必要があります。 次に、EF デザイナーでこのワークフローを **データベース生成ワークフロー** プロパティに割り当てます。 別の方法として、Code First を使用する方が簡単です。

## <a name="other-inheritance-options"></a>その他の継承オプション

型ごとのテーブル (TPT) はもう1つの種類の継承で、データベース内の個別のテーブルが継承に参加するエンティティにマップされます。 EF デザイナーを使用してテーブルごとの継承をマップする方法の詳細については、「 [Ef DESIGNER TPT の継承](xref:ef6/modeling/designer/inheritance/tpt)」を参照してください。

具象型継承 (TPC) と混合継承モデルは、Entity Framework ランタイムによってサポートされていますが、EF デザイナーではサポートされていません。 TPC または混合継承を使用する場合は、Code First を使用する方法と、EDMX ファイルを手動で編集する方法の2つのオプションがあります。 EDMX ファイルを使用することを選択した場合、[マッピングの詳細] ウィンドウは "セーフモード" になり、デザイナーを使用してマッピングを変更することはできません。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するための要件を次に示します。

- Visual Studio の最新バージョン。
- [School サンプルデータベース](xref:ef6/resources/school-database)。

## <a name="set-up-the-project"></a>プロジェクトを設定する

-   Visual Studio 2012 を開きます。
-   **ファイルの選択- &gt; 新規作成- &gt; プロジェクト**
-   左側のウィンドウで、[ **Visual C \# **] をクリックし、**コンソール**テンプレートを選択します。
-   名前として「 **Tphdbfirstsample**」と入力し   ます。
-    **[OK]** を選択します。

## <a name="create-a-model"></a>モデルの作成

-   ソリューションエクスプローラーでプロジェクト名を右クリックし、[追加]、[ ** &gt; 新しい項目**] の順に選択します。
-   左側のメニューから [ **データ** ] を選択し、[テンプレート] ペインで [ **ADO.NET Entity Data Model** ] を選択します。
-   ファイル名として「 **Tphmodel. .edmx** 」と入力し、[ **追加**] をクリックします。
-   [モデルのコンテンツの選択] ダイアログボックスで、[ **データベースから生成**] を選択し、[ **次へ**] をクリックします。
-   [ **新しい接続**] をクリックします。
    [接続プロパティ] ダイアログボックスで、サーバー名 (たとえば、 **(localdb) \\ mssqllocaldb**) を入力し、認証方法を選択します。データベース名として「 **School**」と入力し、[    **OK]** をクリックします。
    [データ接続の選択] ダイアログボックスが、データベース接続の設定によって更新されます。
-   [データベースオブジェクトの選択] ダイアログボックスの [テーブル] ノードで、[ **Person** ] テーブルを選択します。
-   [ **完了**] をクリックします。

モデルを編集するためのデザイン画面を提供する Entity Designer が表示されます。 [データベースオブジェクトの選択] ダイアログボックスで選択したすべてのオブジェクトがモデルに追加されます。

これが、 **Person** テーブルでのデータベースの検索方法です。

![Person テーブル](~/ef6/media/persontable.png) 

## <a name="implement-table-per-hierarchy-inheritance"></a>階層ごとのテーブル継承の実装

 **Person**テーブルには**識別子**列があり、"Student" と "インストラクター" の2つの値のいずれかを持つことができます。 値に応じて、 **Person** テーブルは **Student** エンティティまたは **インストラクター** エンティティにマップされます。  **Person**テーブルには、 **HireDate**と EnrollmentDate という2つの列があります。これは、ユーザーが同時   に学生とインストラクターになることはできません (少なくともこのチュートリアルには含まれません)。 **EnrollmentDate** **nullable**

### <a name="add-new-entities"></a>新しいエンティティの追加

-   新しいエンティティを追加します。
    これを行うには、Entity Framework Designer のデザイン画面の空の領域を右クリックし、[ ** &gt; エンティティの追加**] を選択します。
-   エンティティ名として「 **インストラクター**」   と入力し、基本データ **Entity name**    **Person**    **型**のドロップダウンリストから [Person] を選択します。
-    **[OK]** をクリックします。
-   別の新しいエンティティを追加します。 エンティティ名として「 **Student**    **Entity name**   」と入力し、基本データ **Person**    **型**のドロップダウンリストから [Person] を選択します。

2つの新しいエンティティ型がデザインサーフェイスに追加されました。 矢印は、新しいエンティティ型から **person**   エンティティ型に向いています。これは、 **person**   が新しいエンティティ型の基本型であることを示します。

-   Person エンティティの **HireDate**プロパティを右クリックし    **Person**   ます。 [ **切り取り** ] を選択します (または Ctrl + X キーを使用します)。
-    **インストラクター**エンティティを右クリック   し、[ **貼り付け**] を選択します (または Ctrl + V キーを使用します)。
-    **HireDate**プロパティを右クリック   し、[ **プロパティ**] を選択します。
-   [ **プロパティ**   ] ウィンドウで、[ **null 許容**]   プロパティを [ **false**] に設定します。
-   Person エンティティの **EnrollmentDate**プロパティを右クリックし    **Person**   ます。 [ **切り取り** ] を選択します (または Ctrl + X キーを使用します)。
-    **Student**エンティティを右クリックし、[貼り付け] を選択し **ます (または Ctrl + V キーを使用します)。**
-    **EnrollmentDate**   プロパティを選択し、 **Nullable**   プロパティを **false**に設定します。
-    **Person**   エンティティ型を選択します。 [ **プロパティ**   ] ウィンドウで、[ **Abstract**]   プロパティを [ **true**] に設定します。
-   **Person**から**識別子**のプロパティを削除します。 削除する理由については、次のセクションで説明します。

### <a name="map-the-entities"></a>エンティティのマップ

-    **インストラクター**を右クリックし、[**テーブルマッピング**] を選択します。
    [マッピングの詳細] ウィンドウで [インストラクター] エンティティが選択されます。
-   [マッピングの詳細] ウィンドウで、[ ** &lt; テーブルまたはビュー &gt; の追加**] をクリックし    **Mapping Details**   ます。
    [ ** &lt; テーブルまたはビュー &gt; の追加**] フィールドは、   選択したエンティティをマップできるテーブルまたはビューのドロップダウンリストになります。
-   ドロップダウンリストから [ **Person**] を選択し   ます。
-   [ **マッピングの詳細**   ] ウィンドウが更新され、既定の列マッピングと、条件を追加するためのオプションが表示されます。
-   [ ** &lt; 条件 &gt; の追加**] をクリックします。
    [ ** &lt; &gt; 条件の追加**] フィールドは、条件を   設定できる列のドロップダウンリストになります。
-   ドロップダウンリストから [ **識別子**] を選択し   ます。
-   [ **Operator**    **マッピングの詳細**] ウィンドウの [演算子] 列で   、ドロップダウンリストから [=] を選択します。
-   [ **値/プロパティ** ] 列に「 **インストラクター**」と入力します。 最終的な結果は次のようになります。

    ![マッピングの詳細](~/ef6/media/mappingdetails2.png)

-    **Student**エンティティ型についても同じ手順を繰り返し   ますが、条件は**student**の値と同じにしてください。  
    ***識別子**のプロパティを削除する理由は、テーブルの列を複数回マップできないためです。この列は条件付きマッピングに使用されるため、プロパティマッピングにも使用できません。条件で **Is null**   また **は not null**比較が使用されている場合、どちらの場合でも使用できる唯一の方法です   。*

これで、Table-Per-Hierarchy 継承が実装されました。

![最終 TPH](~/ef6/media/finaltph.png)

## <a name="use-the-model"></a>モデルを使用する

**Main**メソッドが定義されている**Program.cs**ファイルを開きます。 **Main**関数に次のコードを貼り付けます。 このコードは、3つのクエリを実行します。 最初のクエリでは、すべての **Person** オブジェクトが返されます。 2番目のクエリでは、 **OfType** メソッドを使用して、 **インストラクター** オブジェクトを返します。 3番目のクエリでは、 **OfType** メソッドを使用して **Student** オブジェクトを返します。

``` csharp
    using (var context = new SchoolEntities())
    {
        Console.WriteLine("All people:");
        foreach (var person in context.People)
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }

        Console.WriteLine("Instructors only: ");
        foreach (var person in context.People.OfType<Instructor>())
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }

        Console.WriteLine("Students only: ");
        foreach (var person in context.People.OfType<Student>())
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }
    }
```
