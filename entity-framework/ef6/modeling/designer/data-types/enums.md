---
title: Enum サポート-EF デザイナー-EF6
description: Enum Support-Entity Framework 6 の EF デザイナー
author: divega
ms.date: 10/23/2016
ms.assetid: c6ae6d8f-1ace-47db-ad47-b1718f1ba082
uid: ef6/modeling/designer/data-types/enums
ms.openlocfilehash: 316e8160cfd69d9912d63260471eee3df0eb58f6
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620512"
---
# <a name="enum-support---ef-designer"></a>列挙型のサポート-EF デザイナー
> [!NOTE]
> **EF5** 以降: このページで説明した機能、api などは、Entity Framework 5 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。

このビデオとステップバイステップのチュートリアルでは、Entity Framework Designer で列挙型を使用する方法について説明します。 また、LINQ クエリで列挙型を使用する方法も示します。

このチュートリアルでは Model First を使用して新しいデータベースを作成しますが、既存のデータベースにマップするには、EF Designer を [Database First](xref:ef6/modeling/designer/workflows/database-first) ワークフローと共に使用することもできます。

Enum サポートは Entity Framework 5 で導入されました。 列挙型、空間データ型、テーブル値関数などの新機能を使用するには、.NET Framework 4.5 を対象にする必要があります。 既定では、Visual Studio 2012 は .NET 4.5 を対象としています。

Entity Framework では、列挙体には、 **Byte**、 **Int16**、 **Int32**、 **Int64** 、または **SByte**の各基になる型を含めることができます。

## <a name="watch-the-video"></a>ビデオを見る
このビデオでは、Entity Framework Designer で列挙型を使用する方法について説明します。 また、LINQ クエリで列挙型を使用する方法も示します。

**表示者**: ジュリアつい

**ビデオ**: [WMV](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv)  |  [MP4](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v)  |  [WMV (ZIP)](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)

## <a name="pre-requisites"></a>前提条件

このチュートリアルを完了するには、Visual Studio 2012、Ultimate、Premium、Professional、または Web Express エディションがインストールされている必要があります。

## <a name="set-up-the-project"></a>プロジェクトを設定する

1.  Visual Studio 2012 を開く
2.  [**ファイル**] メニューの [**新規作成**] をポイントし、[**プロジェクト**] をクリックします。
3.  左側のウィンドウで、[ **Visual C \# **] をクリックし、**コンソール**テンプレートを選択します。
4.  プロジェクトの名前として「 **Enumefdesigner** 」と入力し、[ **OK]** をクリックします。

## <a name="create-a-new-model-using-the-ef-designer"></a>EF デザイナーを使用して新しいモデルを作成する

1.  ソリューションエクスプローラーでプロジェクト名を右クリックし、[**追加**] をポイントして、[**新しい項目**] をクリックします。
2.  左側のメニューから [ **データ** ] を選択し、[テンプレート] ペインで [ **ADO.NET Entity Data Model** ] を選択します。
3.  ファイル名として「 **Enumtestmodel. .edmx** 」と入力し、[**追加**] をクリックします。
4.  Entity Data Model ウィザードページで、[モデルのコンテンツの選択] ダイアログボックスの [ **空のモデル** ] を選択します。
5.  **[完了]** をクリックします。

モデルを編集するためのデザイン画面を提供する Entity Designer が表示されます。

ウィザードは、次のアクションを実行します。

-   概念モデル、ストレージモデル、およびそれらの間のマッピングを定義する EnumTestModel .edmx ファイルを生成します。 生成されたメタデータファイルがアセンブリに埋め込まれるように、出力アセンブリに埋め込む .edmx ファイルのメタデータ成果物の処理プロパティを設定します。
-   次のアセンブリへの参照を追加します。 EntityFramework、System.componentmodel、および system.object です。
-   EnumTestModel.tt ファイルと EnumTestModel.Context.tt ファイルを作成し、.edmx ファイルの下に追加します。 これらの T4 テンプレートファイルは、.edmx モデル内のエンティティにマップされる DbContext の派生型と POCO 型を定義するコードを生成します。

## <a name="add-a-new-entity-type"></a>新しいエンティティ型の追加

1.  デザイン画面の空の領域を右クリックし、[ ** &gt; エンティティの追加**] を選択すると、[新しいエンティティ] ダイアログボックスが表示されます。
2.  型名に**Department**を指定し、キープロパティ名として**DepartmentID**を指定します。型は**Int32**型のままにします。
3.  **[OK]**
4.  エンティティを右クリックし、[**新しい &gt; スカラープロパティの追加**] を選択します。
5.  新しいプロパティの名前を**Name**に変更します。
6.  新しいプロパティの型を**int32**に変更し (既定では、新しいプロパティは文字列型です)、型を変更し、プロパティウィンドウを開いて、type プロパティを**int32**に変更します。
7.  別のスカラープロパティを追加して、それを**Budget**に変更し、型を**Decimal**に変更します。

## <a name="add-an-enum-type"></a>列挙型の追加

1.  Entity Framework Designer で、[名前] プロパティを右クリックし、[**列挙型への変換**] を選択します。

    ![列挙型への変換](~/ef6/media/converttoenum.png)

2.  [列挙型の **追加** ] ダイアログボックスで、列挙型の名前として「 **DepartmentNames** 」と入力し、基になる型を **Int32**に変更してから、次のメンバーを型に追加します。英語、数学、および経済性

    ![列挙型の追加](~/ef6/media/addenumtype.png)

3.  **[OK]** をクリックします。
4.  モデルを保存してプロジェクトをビルドする
    > [!NOTE]
    > ビルドすると、マップされていないエンティティとアソシエーションに関する警告がエラー一覧に表示されることがあります。 モデルからデータベースを生成することを選択した後、エラーが解消されるため、これらの警告は無視してかまいません。

プロパティウィンドウを見ると、Name プロパティの型が **DepartmentNames** に変更され、新しく追加された列挙型が型のリストに追加されたことがわかります。

[モデルブラウザー] ウィンドウに切り替えた場合は、型が [列挙型] ノードにも追加されていることがわかります。

![モデルブラウザー](~/ef6/media/modelbrowser.png)

>[!NOTE]
> マウスの右ボタンをクリックし、[ **列挙型の追加**] を選択して、このウィンドウから新しい列挙型を追加することもできます。 型が作成されると、型のリストに表示され、プロパティに関連付けることができるようになります。

## <a name="generate-database-from-model"></a>モデルからデータベースを生成

これで、モデルに基づくデータベースを生成できます。

1.  Entity Designer 画面の空の領域を右クリックし、[**モデルからデータベースを生成**] を選択します。
2.  データベース生成ウィザードの [データ接続の選択] ダイアログボックスが表示されたら、[**新しい接続**] ボタンをクリックします。 [サーバー名] に **「(localdb) \\ Mssqllocaldb** 、データベースに**Enumtest**を指定し、[ **OK** ] をクリックします。
3.  新しいデータベースを作成するかどうかを確認するダイアログボックスが表示されたら、[ **はい**] をクリックします。
4.  [ **次へ** ] をクリックすると、データベースの作成ウィザードによって、生成された ddl がデータベースを作成するためのデータ定義言語 (ddl) が生成されます。 [概要と設定] ダイアログボックスには、ddl に列挙型にマップされているテーブルの定義が含まれていないことを確認します。
5.  [ **完了** ] をクリックすると、DDL スクリプトは実行されません。
6.  データベースの作成ウィザードでは、次のことが行われます。 T-sql エディターで **Enumtest. .edmx. sql** が生成され、edmx ファイルのストアスキーマとマッピングセクションによって、App.config ファイルに接続文字列情報が追加されます。
7.  T-sql エディターでマウスの右ボタンをクリック**し、[サーバーへの接続**] ダイアログボックスが表示されたら、手順2の接続情報を入力して [**接続**] をクリックします。
8.  生成されたスキーマを表示するには、SQL Server オブジェクトエクスプローラーでデータベース名を右クリックし、[最新の状態に**更新**] を選択します。

## <a name="persist-and-retrieve-data"></a>データの永続化と取得

Main メソッドが定義されている Program.cs ファイルを開きます。 Main 関数に次のコードを追加します。 このコードは、新しい Department オブジェクトをコンテキストに追加します。 その後、データを保存します。 また、このコードでは、名前が DepartmentNames である Department を返す LINQ クエリも実行されます。

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

```console
DepartmentID: 1 Name: English
```

データベースのデータを表示するには、SQL Server オブジェクトエクスプローラーでデータベース名を右クリックし、[最新の情報に **更新**] を選択します。 次に、テーブルのマウスの右ボタンをクリックし、[ **データの表示**] を選択します。

## <a name="summary"></a>要約

このチュートリアルでは、Entity Framework Designer を使用して列挙型をマップする方法と、コードで enum を使用する方法について説明しました。 
