---
title: クエリ - EF デザイナー - EF6 を定義します。
author: divega
ms.date: 10/23/2016
ms.assetid: e52a297e-85aa-42f6-a922-ba960f8a4b22
ms.openlocfilehash: b1589dc12ccb50754c2e950932a2d82bc4869f6b
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45489480"
---
# <a name="defining-query---ef-designer"></a>クエリ - EF Designer を定義します。
このチュートリアルは、定義を追加する方法を示しますクエリと対応するエンティティを EF Designer を使用してモデルに入力します。 定義クエリは、データベース ビューによって提供されるのと同様の機能を提供するが、データベースではなく、モデルのビューが定義されます。 指定されている SQL ステートメントを実行することができます、定義クエリ、 **DefiningQuery** .edmx ファイルの要素。 詳細については、**DefiningQuery**で、 [SSDL 仕様](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md)を参照してください。

定義クエリを使用する場合は、モデルのエンティティ型を定義する必要があります。 定義クエリによって公開されているデータを表示するエンティティ型が使用されます。 このエンティティの種類を通じて表示されるデータは読み取り専用ことに注意してください。

パラメーター化されたクエリを定義クエリとして実行することはできません。 ただし、データを表示するエンティティ型の挿入、更新、および削除関数をストアド プロシージャにマッピングすると、データを更新できます。 詳細については、[挿入、更新、およびストアド プロシージャを使用した削除](~/ef6/modeling/designer/stored-procedures/cud.md)を参照してください。

このトピックでは、次のタスクを実行する方法を示します。

-   定義クエリを追加します。
-   エンティティ型をモデルに追加します。
-   定義クエリをエンティティ型にマップします。

## <a name="prerequisites"></a>必須コンポーネント

このチュートリアルを完了するための要件を次に示します。

- Visual Studio の最新バージョン。
- [School サンプル データベース](~/ef6/resources/school-database.md)します。

## <a name="set-up-the-project"></a>プロジェクトを設定します。

このチュートリアルでは、Visual Studio 2012 以降を使用しています。

-   Visual Studio を開きます。
-   **[ファイル]** メニューの **[新規作成]** をポイントし、 **[プロジェクト]** をクリックします。
-   左側のウィンドウで次のようにクリックします。 **Visual C\#** を選び、**コンソール アプリケーション**テンプレート。
-   入力**DefiningQuerySample**をクリックして、プロジェクトの名前として**OK**します。

 

## <a name="create-a-model-based-on-the-school-database"></a>School データベースに基づくモデルを作成します。

-   ソリューション エクスプ ローラーでプロジェクト名を右クリックし、[**追加**、] をクリックし、**新しい項目の**します。
-   選択**データ**選択し、左側のメニューから**ADO.NET Entity Data Model**テンプレート ペインでします。
-   入力**DefiningQueryModel.edmx**のファイル名、およびクリック**追加**します。
-   モデルのコンテンツの選択 ダイアログ ボックスで、次のように選択します。**データベースから生成**、 をクリックし、**次**。
-   [新しい接続] をクリックします。 接続のプロパティ ダイアログ ボックスで、サーバー名を入力します (たとえば、 **(localdb)\\mssqllocaldb**) を選択します、認証方式として、型**学校**データベース名、し、。クリックして**OK**します。
    データ接続の選択 ダイアログ ボックスは、データベース接続の設定で更新されます。
-   データベース オブジェクトの選択 ダイアログ ボックスで、確認、**テーブル**ノード。 これをすべてのテーブルに追加されます、**学校**モデル。
-   **[完了]** をクリックします。
-   ソリューション エクスプ ローラーで右クリックし、 **DefiningQueryModel.edmx**ファイルおよび選択**プログラムから開く...**。
-   選択**XML (テキスト) エディター**します。

    ![XML エディター](~/ef6/media/xmleditor.png)

-   クリックして**はい**次のメッセージが表示された場合。

    ![2 の警告](~/ef6/media/warning2.png)

 

## <a name="add-a-defining-query"></a>定義クエリを追加します。

使用するこの手順では、定義を追加する XML エディターのクエリし、エンティティが .edmx ファイルの SSDL セクションに入力します。 

-   追加、 **EntitySet**要素を .edmx ファイル (13 から 5 行目) の SSDL セクション。 次を指定します。
    -   のみ、**名前**と**EntityType**の属性、 **EntitySet**要素が指定されています。
    -   エンティティ型の完全修飾名が使用される、 **EntityType**属性。
    -   実行される SQL ステートメントが指定された、 **DefiningQuery**要素。

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

-   追加、 **EntityType** .edmx ファイルの SSDL セクションに要素。 ファイルに示すように以下です。 次の点に注意してください。
    -   値、**名前**属性の値に対応して、 **EntityType**属性、 **EntitySet** 、上記の要素がの完全修飾名、エンティティ型がで使用される、 **EntityType**属性。
    -   プロパティ名で SQL ステートメントによって返される列名に対応、 **DefiningQuery**要素 (上記)。
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
> 以降を実行する場合、**モデルの更新ウィザード**ダイアログ ボックスで、定義クエリなど、ストレージ モデルに加えられた変更は上書きされます。

 

## <a name="add-an-entity-type-to-the-model"></a>エンティティ型をモデルに追加します。

この手順では、EF デザイナーを使用して概念モデルに、エンティティ型を追加します。  次の点に注意してください。

-   **名前**の値に対応するエンティティの**EntityType**属性、 **EntitySet**上の要素。
-   プロパティ名で SQL ステートメントによって返される列名に対応、 **DefiningQuery**上の要素。
-   この例では、キー値が一意になるように、エンティティ キーは 3 つのプロパティで構成されています。

EF デザイナーでモデルを開きます。

-   DefiningQueryModel.edmx をダブルクリックします。
-   たとえば**はい**に、次のメッセージ。

    ![2 の警告](~/ef6/media/warning2.png)

 

モデルを編集するため、デザイン サーフェイスを提供するエンティティ デザイナーが表示されます。

-   デザイナー サーフェスと選択を右クリックして**新規追加**-&gt;**エンティティ**.
-   指定**GradeReport**エンティティ名と**CourseID**の**キー プロパティ**します。
-   右クリックし、 **GradeReport**エンティティと選択**新規追加**- &gt; **スカラー プロパティ**します。
-   プロパティの既定の名前を変更する**FirstName**します。
-   もう 1 つのスカラー プロパティを追加し、指定**LastName**名。
-   もう 1 つのスカラー プロパティを追加し、指定**グレード**名。
-   **プロパティ**ウィンドウで、変更、**グレード**の**型**プロパティを**Decimal**します。
-   選択、 **FirstName**と**LastName**プロパティ。
-   **プロパティ**ウィンドウで、変更、 **EntityKey**プロパティの値を**True**します。

次の要素が追加された結果として、 **CSDL** .edmx ファイルのセクション。

``` xml
    <EntitySet Name="GradeReport" EntityType="SchoolModel.GradeReport" />

    <EntityType Name="GradeReport">
    . . .
    </EntityType>
```

 

## <a name="map-the-defining-query-to-the-entity-type"></a>定義クエリをエンティティ型にマップします。

この手順では、概念にマップするマッピングの詳細 ウィンドウとストレージ エンティティ型使用しています。

-   右クリックし、 **GradeReport**サーフェスと選択の設計上のエンティティに**テーブル マッピング**します。  
    **マッピングの詳細**ウィンドウが表示されます。
-   選択**GradeReport**から、 **&lt;テーブルまたはビューの追加&gt;** ドロップダウン リスト (下にある**テーブル**秒)。  
    既定の概念の間のマッピングと記憶域**GradeReport**エンティティ型が表示されます。  
    ![マッピング Details3](~/ef6/media/mappingdetails.png)

結果として、 **EntitySetMapping**要素は、.edmx ファイルのマッピング セクションに追加されます。 

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

 

## <a name="call-the-defining-query-in-your-code"></a>コードで定義クエリを呼び出す

使用して、定義クエリを実行できるようになりました、 **GradeReport**エンティティ型。 

``` csharp
    using (var context = new SchoolEntities())
    {
        var report = context.GradeReports.FirstOrDefault();
        Console.WriteLine("{0} {1} got {2}",
            report.FirstName, report.LastName, report.Grade);
    }
```
