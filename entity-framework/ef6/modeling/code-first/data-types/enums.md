---
title: Enum サポート-Code First-EF6
description: Entity Framework 6 の Code First の列挙型サポート
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/data-types/enums
ms.openlocfilehash: e9e1412afb097cc4c944be63130dba0496a80f14
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073978"
---
# <a name="enum-support---code-first"></a>列挙型のサポート-Code First
> [!NOTE]
> **EF5** 以降: このページで説明した機能、api などは、Entity Framework 5 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。

このビデオとステップバイステップのチュートリアルでは、Entity Framework Code First で列挙型を使用する方法について説明します。 また、LINQ クエリで列挙型を使用する方法も示します。

このチュートリアルでは Code First を使用して新しいデータベースを作成しますが、Code First を使用して [既存のデータベースにマップ](xref:ef6/modeling/code-first/workflows/existing-database)することもできます。

Enum サポートは Entity Framework 5 で導入されました。 列挙型、空間データ型、テーブル値関数などの新機能を使用するには、.NET Framework 4.5 を対象にする必要があります。 既定では、Visual Studio 2012 は .NET 4.5 を対象としています。

Entity Framework では、列挙体には、 **Byte**、 **Int16**、 **Int32**、 **Int64** 、または **SByte**の各基になる型を含めることができます。

## <a name="watch-the-video"></a>ビデオを見る
このビデオでは、Entity Framework Code First で列挙型を使用する方法について説明します。 また、LINQ クエリで列挙型を使用する方法も示します。

**表示者**: ジュリアつい

**ビデオ**: [WMV](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.wmv)  |  [MP4](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-mp4video-enumwithcodefirst.m4v)  |  [WMV (ZIP)](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.zip)

## <a name="pre-requisites"></a>前提条件

このチュートリアルを完了するには、Visual Studio 2012、Ultimate、Premium、Professional、または Web Express エディションがインストールされている必要があります。

 

## <a name="set-up-the-project"></a>プロジェクトを設定する

1.  Visual Studio 2012 を開く
2.  [**ファイル**] メニューの [**新規作成**] をポイントし、[**プロジェクト**] をクリックします。
3.  左側のウィンドウで、[ **Visual C \# **] をクリックし、**コンソール**テンプレートを選択します。
4.  プロジェクトの名前として「 **Enumcodefirst** 」と入力し、[ **OK]** をクリックします。

## <a name="define-a-new-model-using-code-first"></a>Code First を使用して新しいモデルを定義する

Code First 開発を使用する場合は、通常、概念 (ドメイン) モデルを定義する .NET Framework クラスを記述することから始めます。 次のコードでは、Department クラスを定義しています。

このコードでは、DepartmentNames 列挙型も定義されています。 既定では、列挙は **int** 型です。 Department クラスの Name プロパティは、DepartmentNames 型です。

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
 

## <a name="define-the-dbcontext-derived-type"></a>DbContext の派生型を定義する

エンティティを定義するだけでなく、DbContext から派生して DbSet&lt;TEntity&gt; プロパティを公開するクラスを定義する必要があります。 DbSet&lt;TEntity&gt; プロパティにより、モデルに含める型をコンテキストに認識させることができます。

DbContext の派生型のインスタンスによって、実行時にエンティティ オブジェクトが管理されます。これには、オブジェクトへのデータベースのデータの設定、変更の追跡、データベースへのデータの保持が含まれます。

DbContext および Dbcontext 型は EntityFramework アセンブリで定義されています。 ここでは、EntityFramework NuGet パッケージを使用して、この DLL への参照を追加します。

1.  ソリューションエクスプローラーで、プロジェクト名を右クリックします。
2.  [ **NuGet パッケージの管理...** ] を選択します。
3.  [NuGet パッケージの管理] ダイアログで、[ **オンライン** ] タブを選択し、 **entityframework** パッケージを選択します。
4.  **[Install]** (インストール) をクリックします。

EntityFramework アセンブリに加えて、System.componentmodel アセンブリおよび system.object アセンブリへの参照も追加されることに注意してください。

Program.cs ファイルの先頭に、次の using ステートメントを追加します。

``` csharp
using System.Data.Entity;
```

Program.cs で、コンテキスト定義を追加します。 

``` csharp
public partial class EnumTestContext : DbContext
{
    public DbSet<Department> Departments { get; set; }
}
```
 

## <a name="persist-and-retrieve-data"></a>データの永続化と取得

Main メソッドが定義されている Program.cs ファイルを開きます。 Main 関数に次のコードを追加します。 このコードは、新しい Department オブジェクトをコンテキストに追加します。 その後、データを保存します。 また、このコードでは、名前が DepartmentNames である Department を返す LINQ クエリも実行されます。

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
 

## <a name="view-the-generated-database"></a>生成されたデータベースを表示する

アプリケーションを初めて実行すると、Entity Framework によってデータベースが作成されます。 Visual Studio 2012 がインストールされているため、データベースは LocalDB インスタンス上に作成されます。 既定では、Entity Framework は、派生コンテキストの完全修飾名の後にデータベースに名前を付けます (この例では、 **Enumcodefirst**)。 その後、既存のデータベースが使用されます。  

データベースの作成後にモデルに変更を加えた場合は、Code First Migrations を使用してデータベーススキーマを更新する必要があることに注意してください。 移行の使用例については [、「新しいデータベースへの Code First](xref:ef6/modeling/code-first/workflows/new-database) 」を参照してください。

データベースとデータを表示するには、次の手順を実行します。

1.  Visual Studio 2012 のメインメニューで、[SQL Server オブジェクトエクスプローラーの**表示**] を選択し  - &gt; **SQL Server Object Explorer**ます。
2.  LocalDB がサーバーの一覧にない場合は、 **SQL Server** で右マウスボタンをクリックして [追加] を選択 **SQL Server** 、既定の **Windows 認証** を使用して localdb インスタンスに接続します。
3.  LocalDB ノードを展開します。
4.  [ **データベース** ] フォルダーを展開して新しいデータベースを表示し、 **Department** テーブルに参照します。ただし、列挙型にマップするテーブルは作成されません Code First
5.  データを表示するには、テーブルを右クリックし、[**データの表示**] を選択します。

## <a name="summary"></a>まとめ

このチュートリアルでは、Entity Framework Code First で列挙型を使用する方法について説明しました。 
