---
title: 空間 Code First-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d617aed1-15f2-48a9-b187-186991c666e3
ms.openlocfilehash: 018f480c1f0f1e74fc9f7a8950a6880e96f1facc
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182650"
---
# <a name="spatial---code-first"></a>空間 Code First
> [!NOTE]
> **EF5**以降: このページで説明した機能、api などは、Entity Framework 5 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。

ビデオとステップバイステップのチュートリアルでは、空間型を Entity Framework Code First にマップする方法について説明します。 また、LINQ クエリを使用して2つの場所の間の距離を検索する方法も示します。

このチュートリアルでは Code First を使用して新しいデータベースを作成しますが、[既存のデータベースに Code First を](~/ef6/modeling/code-first/workflows/existing-database.md)使用することもできます。

空間型のサポートは Entity Framework 5 で導入されました。 空間型、列挙型、テーブル値関数などの新機能を使用するには、.NET Framework 4.5 をターゲットにする必要があることに注意してください。 既定では、Visual Studio 2012 は .NET 4.5 を対象としています。

空間データ型を使用するには、空間をサポートする Entity Framework プロバイダーも使用する必要があります。 詳細については、「[プロバイダーによる空間型のサポート](~/ef6/fundamentals/providers/spatial-support.md)」を参照してください。

主な空間データ型には、geography と geometry の2つがあります。 Geography データ型には、楕円体データ (GPS の緯度や経度の座標など) が格納されます。 Geometry データ型は、ユークリッド (平面) 座標系を表します。

## <a name="watch-the-video"></a>ビデオを見る
このビデオでは、空間型を Entity Framework Code First にマップする方法について説明します。 また、LINQ クエリを使用して2つの場所の間の距離を検索する方法も示します。

**表示者**:ジュリアつい

**ビデオ**:[WMV](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.wmv) | [MP4](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-mp4video-spatialwithcodefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.zip)

## <a name="pre-requisites"></a>前提条件

このチュートリアルを完了するには、Visual Studio 2012、Ultimate、Premium、Professional、または Web Express エディションがインストールされている必要があります。

## <a name="set-up-the-project"></a>プロジェクトを設定する

1.  Visual Studio 2012 を開く
2.  **[ファイル]** メニューの **[新規作成]** をポイントし、 **[プロジェクト]** をクリックします。
3.  左側のウィンドウで、 **[Visual C @ no__t-1]** をクリックし、**コンソール**テンプレートを選択します。
4.  プロジェクトの名前として「 **SpatialCodeFirst** 」と入力し、[ **OK]** をクリックします。

## <a name="define-a-new-model-using-code-first"></a>Code First を使用して新しいモデルを定義する

Code First 開発を使用する場合は、通常、概念 (ドメイン) モデルを定義する .NET Framework クラスを記述することから始めます。 次のコードは、大学クラスを定義しています。

この大学は、DbGeography 型の Location プロパティを持っています。 DbGeography 型を使用するには、system.string アセンブリへの参照を追加し、さらに、system.string using ステートメントも追加する必要があります。

Program.cs ファイルを開き、次の using ステートメントをファイルの先頭に貼り付けます。

``` csharp
using System.Data.Spatial;
```

次の大学クラス定義を Program.cs ファイルに追加します。

``` csharp
public class University  
{
    public int UniversityID { get; set; }
    public string Name { get; set; }
    public DbGeography Location { get; set; }
}
```

## <a name="define-the-dbcontext-derived-type"></a>DbContext の派生型を定義する

エンティティを定義するだけでなく、DbContext から派生し、Dbcontext @ no__t-0TEntity @ no__t プロパティを公開するクラスを定義する必要があります。 DbSet @ no__t-0TEntity @ no__t-1 プロパティを使用すると、モデルに含める型をコンテキストに認識させることができます。

DbContext の派生型のインスタンスは、実行時にエンティティオブジェクトを管理します。これには、データベースからのデータを使用したオブジェクトの読み込み、変更の追跡、およびデータベースへのデータの永続化が含まれます。

DbContext および Dbcontext 型は EntityFramework アセンブリで定義されています。 ここでは、EntityFramework NuGet パッケージを使用して、この DLL への参照を追加します。

1.  ソリューションエクスプローラーで、プロジェクト名を右クリックします。
2.  **[NuGet パッケージの管理...]** を選択します。
3.  NuGet パッケージの管理 ダイアログで、**オンライン** タブを選択し、 **entityframework**パッケージを選択します。
4.  **[インストール]** をクリックします。

EntityFramework アセンブリに加えて、System.componentmodel アセンブリへの参照も追加されることに注意してください。

Program.cs ファイルの先頭に、次の using ステートメントを追加します。

``` csharp
using System.Data.Entity;
```

Program.cs で、コンテキスト定義を追加します。 

``` csharp
public partial class UniversityContext : DbContext
{
    public DbSet<University> Universities { get; set; }
}
```

## <a name="persist-and-retrieve-data"></a>データの永続化と取得

Main メソッドが定義されている Program.cs ファイルを開きます。 Main 関数に次のコードを追加します。

このコードでは、2つの新しい大学オブジェクトをコンテキストに追加します。 空間プロパティは、FromText メソッドを使用して初期化されます。 WellKnownText として表される geography ポイントがメソッドに渡されます。 次に、データを保存します。 次に、指定した場所に最も近い大学オブジェクトを返す LINQ クエリを構築して実行します。

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

```console
The closest University to you is: School of Fine Art.
```

## <a name="view-the-generated-database"></a>生成されたデータベースを表示する

アプリケーションを初めて実行すると、Entity Framework によってデータベースが作成されます。 Visual Studio 2012 がインストールされているため、データベースは LocalDB インスタンス上に作成されます。 既定では、Entity Framework は、派生コンテキストの完全修飾名 (この例では**UniversityContext**) の後にデータベースに名前を付けます。 その後、既存のデータベースが使用されます。  

データベースの作成後にモデルに変更を加えた場合は、Code First Migrations を使用してデータベーススキーマを更新する必要があることに注意してください。 移行の使用例については[、「新しいデータベースへの Code First](~/ef6/modeling/code-first/workflows/new-database.md) 」を参照してください。

データベースとデータを表示するには、次の手順を実行します。

1.  Visual Studio 2012 のメインメニューで、@no__t の**表示**-1 @ no__t-2 **SQL Server オブジェクトエクスプローラー** を選択します。
2.  LocalDB がサーバーの一覧にない場合は、 **SQL Server**で右マウスボタンをクリックし、[追加] を選択**SQL Server**既定の**Windows 認証**を使用して localdb インスタンスに接続します。
3.  LocalDB ノードを展開します。
4.  **[データベース]** フォルダーを展開して新しいデータベースを表示し、 **[大学]** テーブルを参照します。
5.  データを表示するには、テーブルを右クリックし、 **[データの表示]** を選択します。

## <a name="summary"></a>まとめ

このチュートリアルでは、Entity Framework Code First で空間型を使用する方法について説明しました。 
