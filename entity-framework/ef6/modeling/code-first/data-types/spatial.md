---
title: 空間 - EF6 で Code First
author: divega
ms.date: 10/23/2016
ms.assetid: d617aed1-15f2-48a9-b187-186991c666e3
ms.openlocfilehash: 744ea58c7a8796ed20adc304b3928551eb6928a4
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490598"
---
# <a name="spatial---code-first"></a>空間の最初のコード
> [!NOTE]
> **EF5 以降のみ**-機能、Api、Entity Framework 5 で導入されたなどのこのページで説明します。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。

ビデオ、およびステップ バイ ステップ チュートリアルでは、Entity Framework Code First での空間型をマップする方法を示します。 LINQ クエリを使用して、2 つの場所間の距離を検索する方法も示します。

このチュートリアルは、新しいデータベースを作成する Code First では使用が、使用することも[既存のデータベースを Code First](~/ef6/modeling/code-first/workflows/existing-database.md)します。

空間型のサポートは、Entity Framework 5 で導入されました。 空間型、列挙型、およびテーブル値関数などの新機能を使用する .NET Framework 4.5 をターゲットする必要がありますに注意してください。 Visual Studio 2012 では、既定では .NET 4.5 を対象とします。

空間データ型を使用するには、空間をサポートする Entity Framework プロバイダーを使用することも必要があります。 参照してください[空間型のプロバイダー サポート](~/ef6/fundamentals/providers/spatial-support.md)詳細についてはします。

2 つのメインの空間データの種類があります: geography および geometry します。 Geography データ型の楕円体データを格納する (たとえば、GPS の緯度と経度座標)。 Geometry データ型は、ユークリッド (平面) 座標系を表します。

## <a name="watch-the-video"></a>ビデオを見る
このビデオでは、Entity Framework Code First での空間型をマップする方法を示します。 LINQ クエリを使用して、2 つの場所間の距離を検索する方法も示します。

**によって提示される**: Julia Kornich

**ビデオ**: [WMV](http://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.wmv) | [MP4](http://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-mp4video-spatialwithcodefirst.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.zip)

## <a name="pre-requisites"></a>前提条件

このチュートリアルを完了にインストールされている Visual Studio 2012、Ultimate、Premium、Professional、または Web Express edition を使用する必要があります。

## <a name="set-up-the-project"></a>プロジェクトを設定します。

1.  Visual Studio 2012 を開く
2.  **ファイル**メニューで、**新規**、 をクリックし、**プロジェクト**
3.  左側のウィンドウで次のようにクリックします**Visual C\#** を選び、**コンソール**テンプレート。
4.  入力**SpatialCodeFirst**をクリックして、プロジェクトの名前として**OK**

## <a name="define-a-new-model-using-code-first"></a>Code First を使用して新しいモデルを定義します。

Code First の開発を使用する場合に、(ドメイン) の概念モデルを定義する .NET Framework クラスを記述することで、通常で開始します。 次のコードは、大学のクラスを定義します。

大学では、DbGeography 型の場所のプロパティがあります。 DbGeography の型を使用するには、System.Data.Entity アセンブリへの参照を追加しもステートメントを使用して System.Data.Spatial を追加する必要があります。

Program.cs ファイルを開き、次を貼り付け、ファイルの上部にあるステートメントを使用します。

``` csharp
using System.Data.Spatial;
```

Program.cs ファイルに次の大学クラス定義を追加します。

``` csharp
public class University  
{
    public int UniversityID { get; set; }
    public string Name { get; set; }
    public DbGeography Location { get; set; }
}
```

## <a name="define-the-dbcontext-derived-type"></a>定義、DbContext 派生型

エンティティを定義するだけでなくを DbContext から派生し、DbSet を公開するクラスを定義する必要があります。&lt;TEntity&gt;プロパティ。 DbSet&lt;TEntity&gt;プロパティは、モデルに追加する型も把握してコンテキストを使用します。

DbContext 派生型のインスタンスがデータベースからデータを使用してオブジェクトの設定が含まれています。 ランタイム処理中に、エンティティ オブジェクトを管理、追跡、および永続化するデータをデータベースに変更します。

DbContext、DbSet の型は、EntityFramework アセンブリで定義されます。 EntityFramework NuGet パッケージを使用してこの DLL への参照を追加します。

1.  ソリューション エクスプ ローラーでプロジェクト名を右クリックします。
2.  選択**NuGet パッケージを管理しています.**
3.  NuGet パッケージの管理 ダイアログ ボックスで、、**オンライン** タブで選択し、 **EntityFramework**パッケージ。
4.  クリックして**インストール**

EntityFramework アセンブリだけでなく System.ComponentModel.DataAnnotations アセンブリへの参照も追加されたことに注意してください。

次のコードを追加、Program.cs ファイルの上部にあるステートメントを使用します。

``` csharp
using System.Data.Entity;
```

Program.cs では、コンテキストの定義を追加します。 

``` csharp
public partial class UniversityContext : DbContext
{
    public DbSet<University> Universities { get; set; }
}
```

## <a name="persist-and-retrieve-data"></a>永続化およびデータの取得

Main メソッドが定義されている Program.cs ファイルを開きます。 Main 関数には、次のコードを追加します。

コードでは、コンテキストに新しい大学の 2 つのオブジェクトを追加します。 空間プロパティは、DbGeography.FromText メソッドを使用して初期化されます。 地理的ポイント WellKnownText がメソッドに渡されるとして表されます。 コードは、データを保存します。 次に、LINQ クエリでその場所が指定した場所に最も近い大学オブジェクトを返す構成および実行します。

``` csharp
using (var context = new UniversityContext ())
{
    context.Universities.Add(new University()
        {
            Name = "Graphic Design Institute",
            Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
        });

    context. Universities.Add(new University()
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

## <a name="view-the-generated-database"></a>生成されたデータベースを表示します。

初めてアプリケーションを実行すると、データベースは、Entity Framework によって作成されます。 Visual Studio 2012 をインストールしたため、データベースが LocalDB インスタンスで作成されます。 既定では、Entity Framework は、派生コンテキストの完全修飾名の後にデータベースを名前 (この例では**SpatialCodeFirst.UniversityContext**)。 既存のデータベースが使用される後続の時間。  

データベースが作成された後に、モデルに変更を加えた場合 Code First Migrations を使用データベース スキーマを更新する必要がありますに注意してください。 参照してください[新しいデータベースを Code First](~/ef6/modeling/code-first/workflows/new-database.md) Migrations を使用しての例についてはします。

データベースとデータを表示するには、次の操作を行います。

1.  Visual Studio 2012 のメイン メニューで、次のように選択します。**ビュー**  - &gt; **SQL Server オブジェクト エクスプ ローラー**します。
2.  サーバーの一覧で、LocalDB でない場合にマウスの右ボタンをクリックします**SQL Server**選択と**SQL Server の追加**既定値を使用して、 **Windows 認証**への接続に、。LocalDB インスタンス
3.  LocalDB のノードを展開します。
4.  Unfold、**データベース**フォルダーを新しいデータベースを表示しを参照、**大学**テーブル
5.  データを表示し、テーブルを右クリックして、選択**データの表示**

## <a name="summary"></a>まとめ

このチュートリアルでは、Entity Framework Code First で空間型を使用する方法を説明しました。 
