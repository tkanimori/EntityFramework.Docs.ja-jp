---
title: 列挙型のサポート - EF デザイナー - EF6
author: divega
ms.date: 2016-10-23
ms.assetid: c6ae6d8f-1ace-47db-ad47-b1718f1ba082
ms.openlocfilehash: a94a497e8c5b3213dd7eb4215de90164d437507d
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2018
ms.locfileid: "44250636"
---
# <a name="enum-support---ef-designer"></a>列挙型のサポート - EF Designer
> [!NOTE]
> **EF5 以降のみ**-機能、Api、Entity Framework 5 で導入されたなどのこのページで説明します。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。

このビデオ、およびステップ バイ ステップ チュートリアルでは、Entity Framework デザイナーを使用した列挙型を使用する方法を示します。 LINQ クエリで列挙型を使用する方法も示します。

このチュートリアルで、新しいデータベースを作成する最初のモデル使用しますが、EF デザイナーはでも使用できます、 [Database First](~/ef6/modeling/designer/workflows/database-first.md)ワークフローに既存のデータベースにマップします。

列挙型のサポートは、Entity Framework 5 で導入されました。 列挙型、空間データ型、およびテーブル値関数などの新機能を使用するには、.NET Framework 4.5 を対象する必要があります。 Visual Studio 2012 では、既定では .NET 4.5 を対象とします。

列挙体では、Entity Framework では、次の基になる型を持つことができます:**バイト**、 **Int16**、 **Int32**、 **Int64** 、または**SByte**します。

## <a name="watch-the-video"></a>ビデオを見る
このビデオでは、Entity Framework デザイナーを使用した列挙型を使用する方法を示します。 LINQ クエリで列挙型を使用する方法も示します。

**によって提示される**: Julia Kornich

**ビデオ**: [WMV](http://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv) | [MP4](http://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)

## <a name="pre-requisites"></a>前提条件

このチュートリアルを完了にインストールされている Visual Studio 2012、Ultimate、Premium、Professional、または Web Express edition を使用する必要があります。

## <a name="set-up-the-project"></a>プロジェクトを設定します。

1.  Visual Studio 2012 を開く
2.  **ファイル**メニューで、**新規**、 をクリックし、**プロジェクト**
3.  左側のウィンドウで次のようにクリックします**Visual C\#** を選び、**コンソール**テンプレート。
4.  入力**EnumEFDesigner**をクリックして、プロジェクトの名前として**OK**

## <a name="create-a-new-model-using-the-ef-designer"></a>EF デザイナーを使用して、新しいモデルを作成します。

1.  ソリューション エクスプ ローラーでプロジェクト名を右クリックし、[**追加**、] をクリックし、**新しい項目**
2.  選択**データ**選択し、左側のメニューから**ADO.NET Entity Data Model**テンプレート ペイン
3.  入力**EnumTestModel.edmx**のファイル名、およびクリック**追加**
4.  Entity Data Model ウィザード ページで、次のように選択します**空のモデル**モデルのコンテンツの選択 ダイアログ ボックス。
5.  クリックして**完了**

モデルを編集するため、デザイン サーフェイスを提供するエンティティ デザイナーが表示されます。

ウィザードは、次のアクションを実行します。

-   概念モデル、ストレージ モデル、およびそれらの間のマッピングを定義する EnumTestModel.edmx ファイルを生成します。 生成されたメタデータ ファイルがアセンブリに埋め込まを取得するために、出力アセンブリに埋め込むに .edmx ファイルの Metadata Artifact Processing プロパティを設定します。
-   次のアセンブリへの参照を追加します: EntityFramework、System.ComponentModel.DataAnnotations、System.Data.Entity します。
-   EnumTestModel.tt と EnumTestModel.Context.tt ファイルを作成し、.edmx ファイルで追加します。 これらの T4 テンプレート ファイルは、DbContext 派生型と .edmx モデル内のエンティティにマップされる POCO 型を定義するコードを生成します。

## <a name="add-a-new-entity-type"></a>新しいエンティティ型を追加します。

1.  デザイン画面の空の領域を右クリックして**追加 -&gt;エンティティ**、新しいエンティティのダイアログ ボックスが表示されます
2.  指定**部門**型の名前を指定し、指定**DepartmentID**キー プロパティの名前と型をそのまま使用**Int32**
3.  **[OK]** をクリックします。
4.  エンティティを右クリックして **- 新しい追加&gt;スカラー プロパティ**
5.  新しいプロパティの名前を変更**名**
6.  新しいプロパティの型を変更**Int32** (既定では、新しいプロパティが文字列型の) 型を変更するプロパティ ウィンドウを開くし、型のプロパティを変更**Int32**
7.  もう 1 つのスカラー プロパティを追加し、名前を変更する**予算**に種類を変更**10 進数**

## <a name="add-an-enum-type"></a>列挙型を追加します。

1.  Entity Framework デザイナーでは、Name プロパティを右クリックして**列挙型に変換**

    ![列挙型に変換します。](~/ef6/media/converttoenum.png)

2.  **列挙の追加** ダイアログ ボックスに「 **DepartmentNames**列挙型の名前の変更を基になる型**Int32**、型には次メンバーを追加および: 英語、数値演算と経済性

    ![列挙型を追加します。](~/ef6/media/addenumtype.png)

3.  キーを押して**OK**
4.  モデルを保存し、プロジェクトをビルド
    > [!NOTE]
    > ビルドすると、マップされていないエンティティとアソシエーションに関する警告がエラー一覧に表示されます。 エラーが解消されますモデルからデータベースを生成するように選択した後にあるために、これらの警告を無視できます。

[プロパティ] ウィンドウを確認することがわかりますに Name プロパティの型が変更されたこと**DepartmentNames**の種類の一覧に新しく追加された列挙型が追加されたとします。

モデル ブラウザー ウィンドウに切り替えた場合、型が列挙型のノードにも追加されたことが表示されます。

![モデル ブラウザー](~/ef6/media/modelbrowser.png)

>[!NOTE]
> マウスの右ボタンをクリックして、このウィンドウから新しい列挙型を追加することも**列挙型の追加**します。 型を作成した後の種類の一覧に表示され、プロパティに関連付けることができます。

## <a name="generate-database-from-model"></a>モデルからデータベースを生成します。

モデルに基づいているデータベースを生成できます。

1.  エンティティ デザイナー サーフェスと選択を空の領域を右クリックして**モデルからのデータベースの生成**
2.  データベースの生成ウィザードのデータ接続ダイアログ ボックスが選択の表示 をクリックして、**新しい接続**ボタンを指定する **(localdb)\\mssqllocaldb**サーバー名および**EnumTest** 、データベースとクリック**OK**
3.  新しいデータベースを作成するかどうかたずねるダイアログがポップアップ表示をクリックします**はい**します。
4.  をクリックして**次**概要、および DDL の定義が含まれていないの設定 ダイアログ ボックス注に表示されますが、データベースを作成するには、生成された DDL をデータベースの作成ウィザードがデータ定義言語 (DDL) を生成し、列挙型にマップされるテーブル
5.  クリックして**完了**完了 をクリックしても、DDL スクリプトは実行されません。
6.  データベースの作成ウィザードは、次: 開きます、 **EnumTest.edmx.sql** T-SQL エディターの生成で EDMX のストアのスキーマとマッピング セクションでは App.config ファイルに追加の接続文字列情報をファイル
7.  T-SQL エディターでマウスの右ボタンをクリックし、選択**Execute**サーバー ダイアログへの接続が表示されたら、手順 2. の接続情報を入力し、クリックして**接続**
8.  生成されたスキーマを表示するには、SQL Server オブジェクト エクスプ ローラーでデータベース名を右クリックして**更新**

## <a name="persist-and-retrieve-data"></a>永続化およびデータの取得

Main メソッドが定義されている Program.cs ファイルを開きます。 Main 関数には、次のコードを追加します。 コードでは、コンテキストに新しい部門オブジェクトを追加します。 データが保存されます。 コードでは、名前は DepartmentNames.English 部門を返す LINQ クエリも実行します。

``` csharp
using (var context = new EnumTestModelContainer())
{
    context.Departments.Add(new Department{ Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} and Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

アプリケーションをコンパイルして実行します。 このプログラムの出力は、次のようになります。

```
DepartmentID: 1 Name: English
```

データベースにデータを表示するには、SQL Server オブジェクト エクスプ ローラーでデータベース名を右クリックして**更新**します。 マウスの右ボタンをクリックし、テーブルをクリックし、**ビュー データ**します。

## <a name="summary"></a>まとめ

このチュートリアルでは、Entity Framework デザイナーを使用して列挙型にマップする方法とコードで列挙型を使用する方法について説明しました。 
