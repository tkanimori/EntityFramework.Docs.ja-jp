---
title: 空間-EF デザイナー-EF6
description: Entity Framework 6 の空間 EF デザイナー
author: divega
ms.date: 10/23/2016
ms.assetid: 06baa6e1-d680-4a95-845b-81305c87a962
uid: ef6/modeling/designer/data-types/spatial
ms.openlocfilehash: 10430d6a043182e5536dfad148dc4158cf4c96c9
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620490"
---
# <a name="spatial---ef-designer"></a>空間-EF デザイナー
> [!NOTE]
> **EF5** 以降: このページで説明した機能、api などは、Entity Framework 5 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。

ビデオとステップバイステップのチュートリアルでは、空間型を Entity Framework Designer にマップする方法について説明します。 また、LINQ クエリを使用して2つの場所の間の距離を検索する方法も示します。

このチュートリアルでは Model First を使用して新しいデータベースを作成しますが、既存のデータベースにマップするには、EF Designer を [Database First](xref:ef6/modeling/designer/workflows/database-first) ワークフローと共に使用することもできます。

空間型のサポートは Entity Framework 5 で導入されました。 空間型、列挙型、テーブル値関数などの新機能を使用するには、.NET Framework 4.5 をターゲットにする必要があることに注意してください。 既定では、Visual Studio 2012 は .NET 4.5 を対象としています。

空間データ型を使用するには、空間をサポートする Entity Framework プロバイダーも使用する必要があります。 詳細については、「 [プロバイダーによる空間型のサポート](xref:ef6/fundamentals/providers/spatial-support) 」を参照してください。

主な空間データ型には、geography と geometry の2つがあります。 Geography データ型には、楕円体データ (GPS の緯度や経度の座標など) が格納されます。 Geometry データ型は、ユークリッド (平面) 座標系を表します。

## <a name="watch-the-video"></a>ビデオを見る
このビデオでは、空間型を Entity Framework Designer にマップする方法について説明します。 また、LINQ クエリを使用して2つの場所の間の距離を検索する方法も示します。

**表示者**: ジュリアつい

**ビデオ**: [WMV](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv)  |  [MP4](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v)  |  [WMV (ZIP)](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)

## <a name="pre-requisites"></a>前提条件

このチュートリアルを完了するには、Visual Studio 2012、Ultimate、Premium、Professional、または Web Express エディションがインストールされている必要があります。

## <a name="set-up-the-project"></a>プロジェクトを設定する

1.  Visual Studio 2012 を開く
2.  [**ファイル**] メニューの [**新規作成**] をポイントし、[**プロジェクト**] をクリックします。
3.  左側のウィンドウで、[ **Visual C \# **] をクリックし、**コンソール**テンプレートを選択します。
4.  プロジェクトの名前として「 **SpatialEFDesigner** 」と入力し、[ **OK]** をクリックします。

## <a name="create-a-new-model-using-the-ef-designer"></a>EF デザイナーを使用して新しいモデルを作成する

1.  ソリューションエクスプローラーでプロジェクト名を右クリックし、[**追加**] をポイントして、[**新しい項目**] をクリックします。
2.  左側のメニューから [ **データ** ] を選択し、[テンプレート] ペインで [ **ADO.NET Entity Data Model** ] を選択します。
3.  ファイル名として「UniversityModel」と入力し、[**追加**] をクリックし**ます。**
4.  Entity Data Model ウィザードページで、[モデルのコンテンツの選択] ダイアログボックスの [ **空のモデル** ] を選択します。
5.  **[完了]** をクリックします。

モデルを編集するためのデザイン画面を提供する Entity Designer が表示されます。

ウィザードは、次のアクションを実行します。

-   概念モデル、ストレージモデル、およびそれらの間のマッピングを定義する EnumTestModel .edmx ファイルを生成します。 生成されたメタデータファイルがアセンブリに埋め込まれるように、出力アセンブリに埋め込む .edmx ファイルのメタデータ成果物の処理プロパティを設定します。
-   次のアセンブリへの参照を追加します。 EntityFramework、System.componentmodel、および system.object です。
-   UniversityModel.tt ファイルと UniversityModel.Context.tt ファイルを作成し、.edmx ファイルの下に追加します。 これらの T4 テンプレートファイルによって、.edmx モデル内のエンティティにマップされる DbContext の派生型と POCO 型を定義するコードが生成されます。

## <a name="add-a-new-entity-type"></a>新しいエンティティ型の追加

1.  デザイン画面の空の領域を右クリックし、[ ** &gt; エンティティの追加**] を選択すると、[新しいエンティティ] ダイアログボックスが表示されます。
2.  型名として「**大学**」を指定し、キープロパティ名として「 **UniversityID** 」を指定し、型を**Int32**にします。
3.  **[OK]**
4.  エンティティを右クリックし、[**新しい &gt; スカラープロパティの追加**] を選択します。
5.  新しいプロパティの名前を**Name**に変更します。
6.  別のスカラープロパティを追加し、その名前を「 **Location** 」に変更して、プロパティウィンドウを開き、新しいプロパティの型を**Geography**に変更します。
7.  モデルを保存してプロジェクトをビルドする
    > [!NOTE]
    > ビルドすると、マップされていないエンティティとアソシエーションに関する警告がエラー一覧に表示されることがあります。 モデルからデータベースを生成することを選択した後、エラーが解消されるため、これらの警告は無視してかまいません。

## <a name="generate-database-from-model"></a>モデルからデータベースを生成

これで、モデルに基づくデータベースを生成できます。

1.  Entity Designer 画面の空の領域を右クリックし、[**モデルからデータベースを生成**] を選択します。
2.  データベース生成ウィザードの [データ接続の選択] ダイアログボックスが表示されます。 [**新しい接続**] ボタンをクリックして、データベースのサーバー名と**大学**の** \\ mssqllocaldb (localdb)** を指定し、[ **OK** ] をクリックします。
3.  新しいデータベースを作成するかどうかを確認するダイアログボックスが表示されたら、[ **はい**] をクリックします。
4.  [ **次へ** ] をクリックすると、データベースの作成ウィザードによって、生成された ddl がデータベースを作成するためのデータ定義言語 (ddl) が生成されます。 [概要と設定] ダイアログボックスには、ddl に列挙型にマップされているテーブルの定義が含まれていないことを確認します。
5.  [ **完了** ] をクリックすると、DDL スクリプトは実行されません。
6.  データベースの作成ウィザードでは、次のことが行われます。 T-sql エディターで **UniversityModel** が生成されると、edmx ファイルのストアスキーマとマッピングセクションによって、接続文字列情報が App.config ファイルに追加されます。
7.  T-sql エディターでマウスの右ボタンをクリック**し、[サーバーへの接続**] ダイアログボックスが表示されたら、手順2の接続情報を入力して [**接続**] をクリックします。
8.  生成されたスキーマを表示するには、SQL Server オブジェクトエクスプローラーでデータベース名を右クリックし、[最新の状態に**更新**] を選択します。

## <a name="persist-and-retrieve-data"></a>データの永続化と取得

Main メソッドが定義されている Program.cs ファイルを開きます。 Main 関数に次のコードを追加します。

このコードでは、2つの新しい大学オブジェクトをコンテキストに追加します。 空間プロパティは、FromText メソッドを使用して初期化されます。 WellKnownText として表される geography ポイントがメソッドに渡されます。 次に、データを保存します。 次に、指定した場所に最も近い大学オブジェクトを返す LINQ クエリを構築して実行します。

``` csharp
using (var context = new UniversityModelContainer())
{
    context.Universities.Add(new University()
    {
        Name = "Graphic Design Institute",
        Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
    });

    context.Universities.Add(new University()
    {
        Name = "School of Fine Art",
        Location = DbGeography.FromText("POINT(-122.335197 47.646711)"),
    });

    context.SaveChanges();

    var myLocation = DbGeography.FromText("POINT(-122.296623 47.640405)");

    var university = (from u in context.Universities
                                orderby u.Location.Distance(myLocation)
                                select u).FirstOrDefault();

    Console.WriteLine(
        "The closest University to you is: {0}.",
        university.Name);
}
```

アプリケーションをコンパイルして実行します。 このプログラムの出力は、次のようになります。

```console
The closest University to you is: School of Fine Art.
```

データベースのデータを表示するには、SQL Server オブジェクトエクスプローラーでデータベース名を右クリックし、[最新の情報に **更新**] を選択します。 次に、テーブルのマウスの右ボタンをクリックし、[ **データの表示**] を選択します。

## <a name="summary"></a>要約

このチュートリアルでは、Entity Framework Designer を使用して空間型をマップする方法と、コードで空間型を使用する方法について説明しました。 
