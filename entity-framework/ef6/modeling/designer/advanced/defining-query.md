---
title: クエリの定義-EF デザイナー-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: e52a297e-85aa-42f6-a922-ba960f8a4b22
ms.openlocfilehash: b1589dc12ccb50754c2e950932a2d82bc4869f6b
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415533"
---
# <a name="defining-query---ef-designer"></a>クエリの定義-EF デザイナー
このチュートリアルでは、EF デザイナーを使用して、定義クエリとそれに対応するエンティティ型をモデルに追加する方法について説明します。 定義クエリは、データベースビューと同様の機能を提供するために一般的に使用されますが、ビューはデータベースではなくモデルで定義されます。 定義クエリを使用すると、.edmx ファイルの **DefiningQuery** 要素で指定された SQL ステートメントを実行できます。 詳細については、「 **DefiningQuery** In The [SSDL Specification](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md)」を参照してください。

クエリの定義を使用する場合は、モデルでエンティティ型を定義する必要もあります。 エンティティ型は、定義クエリによって公開されるデータを表示するために使用されます。 このエンティティ型を通じて表示されるデータは読み取り専用であることに注意してください。

パラメーター化されたクエリを定義クエリとして実行することはできません。 ただし、データを表示するエンティティ型の挿入、更新、および削除関数をストアド プロシージャにマッピングすると、データを更新できます。 詳細については、「 [ストアドプロシージャを使用した挿入、更新、および削除](~/ef6/modeling/designer/stored-procedures/cud.md)」を参照してください。

このトピックでは、次のタスクを実行する方法について説明します。

-   定義クエリの追加
-   モデルへのエンティティ型の追加
-   定義クエリをエンティティ型にマップする

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するための要件を次に示します。

- Visual Studio の最新バージョン。
- [School サンプルデータベース](~/ef6/resources/school-database.md)。

## <a name="set-up-the-project"></a>プロジェクトを設定する

このチュートリアルでは、Visual Studio 2012 以降を使用しています。

-   Visual Studio を開きます。
-   **[ファイル]** メニューの **[新規作成]** をポイントし、 **[プロジェクト]** をクリックします。
-   左側のウィンドウで、[ **Visual C\#** ] をクリックし、 **[コンソールアプリケーション]** テンプレートを選択します。
-   プロジェクトの名前として「名前」**と入力し**、[ **OK]** をクリックします。

 

## <a name="create-a-model-based-on-the-school-database"></a>School データベースに基づくモデルを作成する

-   ソリューションエクスプローラーでプロジェクト名を右クリックして **[追加]** をポイントし、 **[新しい項目]** をクリックします。
-   左側のメニューから **[データ]** を選択し、テンプレート ペインで  **[ADO.NET Entity Data Model]** を選択します。
-   ファイル名として「**名前」と入力し**、 **[追加]** をクリックします。
-   [モデルのコンテンツの選択] ダイアログボックスで、[ **データベースから生成**] を選択し、[ **次へ**] をクリックします。
-   [新しい接続] をクリックします。 [接続プロパティ] ダイアログボックスで、サーバー名 (たとえば、 **(localdb)\\mssqllocaldb**) を入力し、認証方法を選択します。データベース名として「 **School** 」と入力し、[ **OK**] をクリックします。
    [データ接続の選択] ダイアログボックスが、データベース接続の設定によって更新されます。
-   [データベースオブジェクトの選択] ダイアログボックスで、[**テーブル** ] ノードをオンにします。 これにより、すべてのテーブルが**School**モデルに追加されます。
-   [ **完了**] をクリックします。
-   ソリューションエクスプローラーで、[型の**クエリ**] を右クリックし、[ファイル**を開くアプリケーション**の選択] をクリックします。
-   **XML (テキスト) エディター**を選択します。

    ![XML エディター](~/ef6/media/xmleditor.png)

-   次のメッセージが表示されたら、[**はい]** をクリックします。

    ![警告2](~/ef6/media/warning2.png)

 

## <a name="add-a-defining-query"></a>定義クエリの追加

この手順では、XML エディターを使用して、定義クエリとエンティティ型を .edmx ファイルの SSDL セクションに追加します。 

-   .Edmx ファイルの SSDL セクションに **EntitySet** 要素を追加します (行 5 ~ 13)。 次の指定を行います。
    -    **EntitySet** 要素の属性  **名前** と **EntityType**のみが指定されています。
    -   エンティティ型の完全修飾名は、 **EntityType** 属性で使用されます。
    -   実行される SQL ステートメントは、 **DefiningQuery** 要素で指定されます。

``` xml
    <!-- SSDL content -->
    <edmx:StorageModels>
      <Schema Namespace="SchoolModel.Store" Alias="Self" Provider="System.Data.SqlClient" ProviderManifestToken="2008" xmlns:store="http://schemas.microsoft.com/ado/2007/12/edm/EntityStoreSchemaGenerator" xmlns="http://schemas.microsoft.com/ado/2009/11/edm/ssdl">
        <EntityContainer Name="SchoolModelStoreContainer">
           <EntitySet Name="GradeReport" EntityType="SchoolModel.Store.GradeReport">
              <DefiningQuery>
                SELECT CourseID, Grade, FirstName, LastName
                FROM StudentGrade
                JOIN
                (SELECT * FROM Person WHERE EnrollmentDate IS NOT NULL) AS p
                ON StudentID = p.PersonID
              </DefiningQuery>
          </EntitySet>
          <EntitySet Name="Course" EntityType="SchoolModel.Store.Course" store:Type="Tables" Schema="dbo" />
```

-   **EntityType**要素を .EDMX の SSDL セクションに追加します。 次のようにファイルを表示します。 次のことを考慮してください。
    -   **Name**属性の値は、上の**EntitySet**要素の**entitytype**属性の値に対応します。ただし、 **entitytype**属性ではエンティティ型の完全修飾名が使用されます。
    -   プロパティ名は、 **DefiningQuery**要素 (上記) の SQL ステートメントによって返される列名に対応しています。
    -   この例では、キー値が一意になるように、エンティティ キーは 3 つのプロパティで構成されています。

``` xml
    <EntityType Name="GradeReport">
      <Key>
        <PropertyRef Name="CourseID" />
        <PropertyRef Name="FirstName" />
        <PropertyRef Name="LastName" />
      </Key>
      <Property Name="CourseID"
                Type="int"
                Nullable="false" />
      <Property Name="Grade"
                Type="decimal"
                Precision="3"
                Scale="2" />
      <Property Name="FirstName"
                Type="nvarchar"
                Nullable="false"
                MaxLength="50" />
      <Property Name="LastName"
                Type="nvarchar"
                Nullable="false"
                MaxLength="50" />
    </EntityType>
```

>[!NOTE]
> 後で [モデルの**更新ウィザード**] ダイアログを実行すると、クエリの定義など、ストレージモデルに加えられたすべての変更が上書きされます。

 

## <a name="add-an-entity-type-to-the-model"></a>モデルへのエンティティ型の追加

この手順では、EF デザイナーを使用して、エンティティ型を概念モデルに追加します。  次の点に注意してください。

-   エンティティの**名前**は、上記の**EntitySet**要素の**EntityType**属性の値に対応します。
-   プロパティ名は、上の**DefiningQuery**要素の SQL ステートメントによって返される列名に対応しています。
-   この例では、キー値が一意になるように、エンティティ キーは 3 つのプロパティで構成されています。

EF デザイナーでモデルを開きます。

-   [型のクエリ] をダブルクリックします。
-   次のメッセージが表示され**たら、[はい]** を言います。

    ![警告2](~/ef6/media/warning2.png)

 

モデルを編集するためのデザイン画面を提供する Entity Designer が表示されます。

-   デザイナー画面を右クリックし、 **[追加]** [新しい-&gt;**エンティティ...** ] の順に選択します。
-   エンティティ名には**GradeReport**を、**キープロパティ**には**CourseID**を指定します。
-   **GradeReport**エンティティを右クリックし、 **[追加]** [新しい-&gt;**スカラープロパティ**] の順に選択します。
-   プロパティの既定の名前を**FirstName**に変更します。
-   別のスカラープロパティを追加し、名前に**LastName**を指定します。
-   別のスカラープロパティを追加し、名前に**グレード**を指定します。
-   **[プロパティ]** ウィンドウで、**グレード**の**Type**プロパティを**Decimal**に変更します。
-   **FirstName**プロパティと**LastName**プロパティを選択します。
-   **[プロパティ]** ウィンドウで、 **EntityKey**プロパティ値を**True**に変更します。

その結果、.edmx ファイルの**CSDL**セクションに次の要素が追加されました。

``` xml
    <EntitySet Name="GradeReport" EntityType="SchoolModel.GradeReport" />

    <EntityType Name="GradeReport">
    . . .
    </EntityType>
```

 

## <a name="map-the-defining-query-to-the-entity-type"></a>定義クエリをエンティティ型にマップする

この手順では、[マッピングの詳細] ウィンドウを使用して、概念エンティティ型とストレージエンティティ型をマップします。

-   デザイン画面で**GradeReport**エンティティを右クリックし、 **[テーブルマッピング]** を選択します。  
    **[マッピングの詳細]** ウィンドウが表示されます。
-   [テーブル**またはビューを追加&lt;]&gt;** ドロップダウンリスト ( **[テーブル]** s の下にあります) から **[GradeReport]** を選択します。  
    概念とストレージの**GradeReport**エンティティ型の間の既定のマッピングが表示されます。  
    Details3](~/ef6/media/mappingdetails.png) のマッピングの ![

その結果、 **EntitySetMapping** 要素が .edmx ファイルのマッピングセクションに追加されます。 

``` xml
    <EntitySetMapping Name="GradeReports">
      <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.GradeReport)">
        <MappingFragment StoreEntitySet="GradeReport">
          <ScalarProperty Name="LastName" ColumnName="LastName" />
          <ScalarProperty Name="FirstName" ColumnName="FirstName" />
          <ScalarProperty Name="Grade" ColumnName="Grade" />
          <ScalarProperty Name="CourseID" ColumnName="CourseID" />
        </MappingFragment>
      </EntityTypeMapping>
    </EntitySetMapping>
```

-   アプリケーションをコンパイルします。

 

## <a name="call-the-defining-query-in-your-code"></a>コード内で定義クエリを呼び出す

**GradeReport**エンティティ型を使用して、定義クエリを実行できるようになりました。 

``` csharp
    using (var context = new SchoolEntities())
    {
        var report = context.GradeReports.FirstOrDefault();
        Console.WriteLine("{0} {1} got {2}",
            report.FirstName, report.LastName, report.Grade);
    }
```
