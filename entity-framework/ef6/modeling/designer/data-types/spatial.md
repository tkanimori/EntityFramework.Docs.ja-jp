---
title: 空間の EF デザイナー - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 06baa6e1-d680-4a95-845b-81305c87a962
ms.openlocfilehash: 04ba6e8d4a59816ca31e831b8e466cb1152a3d1b
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490214"
---
# <a name="spatial---ef-designer"></a>空間の EF デザイナー
> [!NOTE]
> **EF5 以降のみ**-機能、Api、Entity Framework 5 で導入されたなどのこのページで説明します。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。

ビデオ、およびステップ バイ ステップ チュートリアルでは、Entity Framework デザイナーで空間型をマップする方法を示します。 LINQ クエリを使用して、2 つの場所間の距離を検索する方法も示します。

このチュートリアルで、新しいデータベースを作成する最初のモデル使用しますが、EF デザイナーはでも使用できます、 [Database First](~/ef6/modeling/designer/workflows/database-first.md)ワークフローに既存のデータベースにマップします。

空間型のサポートは、Entity Framework 5 で導入されました。 空間型、列挙型、およびテーブル値関数などの新機能を使用する .NET Framework 4.5 をターゲットする必要がありますに注意してください。 Visual Studio 2012 では、既定では .NET 4.5 を対象とします。

空間データ型を使用するには、空間をサポートする Entity Framework プロバイダーを使用することも必要があります。 参照してください[空間型のプロバイダー サポート](~/ef6/fundamentals/providers/spatial-support.md)詳細についてはします。

2 つのメインの空間データの種類があります: geography および geometry します。 Geography データ型の楕円体データを格納する (たとえば、GPS の緯度と経度座標)。 Geometry データ型は、ユークリッド (平面) 座標系を表します。

## <a name="watch-the-video"></a>ビデオを見る
このビデオでは、Entity Framework デザイナーで空間型をマップする方法を示します。 LINQ クエリを使用して、2 つの場所間の距離を検索する方法も示します。

**によって提示される**: Julia Kornich

**ビデオ**: [WMV](http://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv) | [MP4](http://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)

## <a name="pre-requisites"></a>前提条件

このチュートリアルを完了にインストールされている Visual Studio 2012、Ultimate、Premium、Professional、または Web Express edition を使用する必要があります。

## <a name="set-up-the-project"></a>プロジェクトを設定します。

1.  Visual Studio 2012 を開く
2.  **ファイル**メニューで、**新規**、 をクリックし、**プロジェクト**
3.  左側のウィンドウで次のようにクリックします**Visual C\#** を選び、**コンソール**テンプレート。
4.  入力**SpatialEFDesigner**をクリックして、プロジェクトの名前として**OK**

## <a name="create-a-new-model-using-the-ef-designer"></a>EF デザイナーを使用して、新しいモデルを作成します。

1.  ソリューション エクスプ ローラーでプロジェクト名を右クリックし、[**追加**、] をクリックし、**新しい項目**
2.  選択**データ**選択し、左側のメニューから**ADO.NET Entity Data Model**テンプレート ペイン
3.  入力**UniversityModel.edmx**のファイル名、およびクリック**追加**
4.  Entity Data Model ウィザード ページで、次のように選択します**空のモデル**モデルのコンテンツの選択 ダイアログ ボックス。
5.  クリックして**完了**

モデルを編集するため、デザイン サーフェイスを提供するエンティティ デザイナーが表示されます。

ウィザードは、次のアクションを実行します。

-   概念モデル、ストレージ モデル、およびそれらの間のマッピングを定義する EnumTestModel.edmx ファイルを生成します。 生成されたメタデータ ファイルがアセンブリに埋め込まを取得するために、出力アセンブリに埋め込むに .edmx ファイルの Metadata Artifact Processing プロパティを設定します。
-   次のアセンブリへの参照を追加します: EntityFramework、System.ComponentModel.DataAnnotations、System.Data.Entity します。
-   UniversityModel.tt と UniversityModel.Context.tt ファイルを作成し、.edmx ファイルで追加します。 これらの T4 テンプレート ファイルは、DbContext 派生型と .edmx モデル内のエンティティにマップされる POCO 型を定義するコードを生成します。

## <a name="add-a-new-entity-type"></a>新しいエンティティ型を追加します。

1.  デザイン画面の空の領域を右クリックして**追加 -&gt;エンティティ**、新しいエンティティのダイアログ ボックスが表示されます
2.  指定**大学**型の名前を指定し、指定**UniversityID**キー プロパティの名前と型をそのまま使用**Int32**
3.  **[OK]** をクリックします。
4.  エンティティを右クリックして **- 新しい追加&gt;スカラー プロパティ**
5.  新しいプロパティの名前を変更**名**
6.  もう 1 つのスカラー プロパティを追加し、名前を変更する**場所**プロパティ ウィンドウを開き、新しいプロパティの型を変更**Geography**
7.  モデルを保存し、プロジェクトをビルド
    > [!NOTE]
    > ビルドすると、マップされていないエンティティとアソシエーションに関する警告がエラー一覧に表示されます。 エラーが解消されますモデルからデータベースを生成するように選択した後にあるために、これらの警告を無視できます。

## <a name="generate-database-from-model"></a>モデルからデータベースを生成します。

モデルに基づいているデータベースを生成できます。

1.  エンティティ デザイナー サーフェスと選択を空の領域を右クリックして**モデルからのデータベースの生成**
2.  データベースの生成ウィザードのデータ接続ダイアログ ボックスが選択の表示 をクリックして、**新しい接続**ボタンを指定する **(localdb)\\mssqllocaldb**サーバー名および**大学**、データベースとクリック**OK**
3.  新しいデータベースを作成するかどうかたずねるダイアログがポップアップ表示をクリックします**はい**します。
4.  をクリックして**次**概要、および DDL の定義が含まれていないの設定 ダイアログ ボックス注に表示されますが、データベースを作成するには、生成された DDL をデータベースの作成ウィザードがデータ定義言語 (DDL) を生成し、列挙型にマップされるテーブル
5.  クリックして**完了**完了 をクリックしても、DDL スクリプトは実行されません。
6.  データベースの作成ウィザードは、次: 開きます、 **UniversityModel.edmx.sql** T-SQL エディターの生成で EDMX のストアのスキーマとマッピング セクションでは App.config ファイルに追加の接続文字列情報をファイル
7.  T-SQL エディターでマウスの右ボタンをクリックし、選択**Execute**サーバー ダイアログへの接続が表示されたら、手順 2. の接続情報を入力し、クリックして**接続**
8.  生成されたスキーマを表示するには、SQL Server オブジェクト エクスプ ローラーでデータベース名を右クリックして**更新**

## <a name="persist-and-retrieve-data"></a>永続化およびデータの取得

Main メソッドが定義されている Program.cs ファイルを開きます。 Main 関数には、次のコードを追加します。

コードでは、コンテキストに新しい大学の 2 つのオブジェクトを追加します。 空間プロパティは、DbGeography.FromText メソッドを使用して初期化されます。 地理的ポイント WellKnownText がメソッドに渡されるとして表されます。 コードは、データを保存します。 次に、LINQ クエリでその場所が指定した場所に最も近い大学オブジェクトを返す構成および実行します。

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

```
The closest University to you is: School of Fine Art.
```

データベースにデータを表示するには、SQL Server オブジェクト エクスプ ローラーでデータベース名を右クリックして**更新**します。 マウスの右ボタンをクリックし、テーブルをクリックし、**ビュー データ**します。

## <a name="summary"></a>まとめ

このチュートリアルでは、Entity Framework デザイナーを使用して空間型をマップする方法とコードで空間型を使用する方法について説明しました。 
