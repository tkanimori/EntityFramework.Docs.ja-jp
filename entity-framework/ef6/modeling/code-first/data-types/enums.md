---
title: 列挙型のサポートには、EF6 で Code First
author: divega
ms.date: 10/23/2016
ms.assetid: 77a42501-27c9-4f4b-96df-26c128021467
ms.openlocfilehash: 986991c2dd9470867aaf5424ecb176d7db172426
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45489506"
---
# <a name="enum-support---code-first"></a>列挙型のサポート - Code First
> [!NOTE]
> **EF5 以降のみ**-機能、Api、Entity Framework 5 で導入されたなどのこのページで説明します。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。

このビデオ、およびステップ バイ ステップ チュートリアルでは、Entity Framework Code First で列挙型を使用する方法を示します。 LINQ クエリで列挙型を使用する方法も示します。

このチュートリアルは、新しいデータベースを作成する Code First では使用が、使用することも[既存のデータベースにマップする Code First](~/ef6/modeling/code-first/workflows/existing-database.md)します。

列挙型のサポートは、Entity Framework 5 で導入されました。 列挙型、空間データ型、およびテーブル値関数などの新機能を使用するには、.NET Framework 4.5 を対象する必要があります。 Visual Studio 2012 では、既定では .NET 4.5 を対象とします。

列挙体では、Entity Framework では、次の基になる型を持つことができます:**バイト**、 **Int16**、 **Int32**、 **Int64** 、または**SByte**します。

## <a name="watch-the-video"></a>ビデオを見る
このビデオでは、Entity Framework Code First で列挙型を使用する方法を説明します。 LINQ クエリで列挙型を使用する方法も示します。

**によって提示される**: Julia Kornich

**ビデオ**: [WMV](http://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.wmv) | [MP4](http://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-mp4video-enumwithcodefirst.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.zip)

## <a name="pre-requisites"></a>前提条件

このチュートリアルを完了にインストールされている Visual Studio 2012、Ultimate、Premium、Professional、または Web Express edition を使用する必要があります。

 

## <a name="set-up-the-project"></a>プロジェクトを設定します。

1.  Visual Studio 2012 を開く
2.  **ファイル**メニューで、**新規**、 をクリックし、**プロジェクト**
3.  左側のウィンドウで次のようにクリックします**Visual C\#** を選び、**コンソール**テンプレート。
4.  入力**EnumCodeFirst**をクリックして、プロジェクトの名前として**OK**

## <a name="define-a-new-model-using-code-first"></a>Code First を使用して新しいモデルを定義します。

Code First の開発を使用する場合に、(ドメイン) の概念モデルを定義する .NET Framework クラスを記述することで、通常で開始します。 次のコードは、部門のクラスを定義します。

コードでは、DepartmentNames 列挙型も定義します。 既定では、列挙型はの**int**型。 部門クラスの名前プロパティは、DepartmentNames 型のです。

Program.cs ファイルを開き、次のクラス定義を貼り付けます。

``` csharp
public enum DepartmentNames
{
    English,
    Math,
    Economics
}     

public partial class Department
{
    public int DepartmentID { get; set; }
    public DepartmentNames Name { get; set; }
    public decimal Budget { get; set; }
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

EntityFramework アセンブリだけでなく System.ComponentModel.DataAnnotations と System.Data.Entity アセンブリへの参照が追加されたことにも注意してください。

次のコードを追加、Program.cs ファイルの上部にあるステートメントを使用します。

``` csharp
using System.Data.Entity;
```

Program.cs では、コンテキストの定義を追加します。 

``` csharp
public partial class EnumTestContext : DbContext
{
    public DbSet<Department> Departments { get; set; }
}
```
 

## <a name="persist-and-retrieve-data"></a>永続化およびデータの取得

Main メソッドが定義されている Program.cs ファイルを開きます。 Main 関数には、次のコードを追加します。 コードでは、コンテキストに新しい部門オブジェクトを追加します。 データが保存されます。 コードでは、名前は DepartmentNames.English 部門を返す LINQ クエリも実行します。

``` csharp
using (var context = new EnumTestContext())
{
    context.Departments.Add(new Department { Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

アプリケーションをコンパイルして実行します。 このプログラムの出力は、次のようになります。

``` csharp
DepartmentID: 1 Name: English
```
 

## <a name="view-the-generated-database"></a>生成されたデータベースを表示します。

初めてアプリケーションを実行すると、データベースは、Entity Framework によって作成されます。 Visual Studio 2012 をインストールしたため、データベースが LocalDB インスタンスで作成されます。 既定では、Entity Framework は、派生コンテキストの完全修飾名の後にデータベースを名前 (この例では、 **EnumCodeFirst.EnumTestContext**)。 既存のデータベースが使用される後続の時間。  

データベースが作成された後に、モデルに変更を加えた場合 Code First Migrations を使用データベース スキーマを更新する必要がありますに注意してください。 参照してください[新しいデータベースを Code First](~/ef6/modeling/code-first/workflows/new-database.md) Migrations を使用しての例についてはします。

データベースとデータを表示するには、次の操作を行います。

1.  Visual Studio 2012 のメイン メニューで、次のように選択します。**ビュー**  - &gt; **SQL Server オブジェクト エクスプ ローラー**します。
2.  サーバーの一覧で、LocalDB でない場合にマウスの右ボタンをクリックします**SQL Server**選択と**SQL Server の追加**既定値を使用して、 **Windows 認証**への接続に、。LocalDB インスタンス
3.  LocalDB のノードを展開します。
4.  Unfold、**データベース**フォルダーを新しいデータベースを表示しを参照、**部門**テーブルの Code First はテーブルを作成できません列挙型にマップされるに注意してください
5.  データを表示し、テーブルを右クリックして、選択**データの表示**

## <a name="summary"></a>まとめ

このチュートリアルでは、Entity Framework Code First と列挙型を使用する方法を説明しました。 
