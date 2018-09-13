---
title: デザイナーの CUD ストアド プロシージャ - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 1e773972-2da5-45e0-85a2-3cf3fbcfa5cf
ms.openlocfilehash: 35a00aa817c8643352c517c233977efd49e3baac
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45489558"
---
# <a name="designer-cud-stored-procedures"></a>デザイナーの CUD ストアド プロシージャ
このステップ バイ ステップ チュートリアルの作成をマップする方法を表示する\\挿入、更新、および Entity Framework デザイナー (EF Designer) を使用してストアド プロシージャにエンティティ型の (CUD) 操作を削除します。  既定では、Entity Framework は、CUD 操作で、SQL ステートメントを自動的に生成されますが、これらの操作をストアド プロシージャをマップすることもできます。  

Code First はサポートされていないことをストアド プロシージャまたは関数のマッピングに注意してください。 ただし、System.Data.Entity.DbSet.SqlQuery メソッドを使用して、ストアド プロシージャまたは関数を呼び出すことができます。 例えば:
``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]");
```

## <a name="considerations-when-mapping-the-cud-operations-to-stored-procedures"></a>ストアド プロシージャをマップする CUD 操作時の考慮事項

CUD 操作をストアド プロシージャにマップする場合は、次の考慮事項が適用されます。 

-   CUD 操作の 1 つは、ストアド プロシージャにマッピングは、する場合は、それらのすべてをマップします。 実行された場合、3 つすべてをマップしない場合、マップされていない操作が失敗**UpdateException**がスローされます。
-   ストアド プロシージャのすべてのパラメーターは、エンティティのプロパティにマップする必要があります。
-   サーバーは、挿入行の主キーの値を生成する場合は、エンティティのキー プロパティにこの値をマップする必要があります。 次の例では、 **InsertPerson**ストアド プロシージャは、ストアド プロシージャの結果セットの一部として新しく作成された主キーを返します。 主キーは、エンティティ キーにマップされます (**PersonID**) を使用して、 **&lt;結果バインドの追加&gt;** EF デザイナーの機能です。
-   ストアド プロシージャの呼び出しは、概念モデルのエンティティにマップされた 1:1 です。 たとえば、概念モデルと、マップで継承階層を実装する場合、CUD はストアド プロシージャの**親**(基本)、**子**(派生) エンティティを保存、 **子**変更の呼び出しだけ、**子**ストアド プロシージャはトリガーされません、**親**のストアド プロシージャが。

## <a name="prerequisites"></a>必須コンポーネント

このチュートリアルを完了するための要件を次に示します。

- Visual Studio の最新バージョン。
- [School サンプル データベース](~/ef6/resources/school-database.md)します。

## <a name="set-up-the-project"></a>プロジェクトを設定します。

-   Visual Studio 2012 を開きます。
-   選択**ファイル -&gt;新機能 -&gt;プロジェクト**
-   左側のウィンドウで次のようにクリックします。 **Visual C\#** を選び、**コンソール**テンプレート。
-   入力**CUDSProcsSample**名として。
-   **[OK]** を選択します。

## <a name="create-a-model"></a>モデルを作成します。

-   ソリューション エクスプ ローラーでプロジェクト名を右クリックして**追加 -&gt;新しい項目の**します。
-   選択**データ**選択し、左側のメニューから**ADO.NET Entity Data Model**テンプレート ペインでします。
-   入力**CUDSProcs.edmx**のファイル名、およびクリック**追加**します。
-   モデルのコンテンツの選択 ダイアログ ボックスで、次のように選択します。**データベースから生成**、 をクリックし、**次**。
-   クリックして**新しい接続**します。 接続のプロパティ ダイアログ ボックスで、サーバー名を入力します (たとえば、 **(localdb)\\mssqllocaldb**) を選択します、認証方式として、型**学校**データベース名、し、。クリックして**OK**します。
    データ接続の選択 ダイアログ ボックスは、データベース接続の設定で更新されます。
-   選択 で、データベース オブジェクト ダイアログ ボックスで、**テーブル**ノードを選択、 **Person**テーブル。
-   また、次のストアド プロシージャの選択、**ストアド プロシージャおよび関数**ノード: **DeletePerson**、 **InsertPerson**、および**UpdatePerson**. 
-   Visual Studio 2012 で EF Designer を起動すると、ストアド プロシージャの一括インポートがサポートしています。 **エンティティ モデルに選択されたストアド プロシージャおよび関数のインポート**が既定でオンになっています。 この例では、挿入、更新、およびエンティティ型を削除するプロシージャを格納しますがため、私たちにインポートしたくないとは、このチェック ボックスをオフにします。 

    ![S Procs をインポートします。](~/ef6/media/importsprocs.jpg)

-   **[完了]** をクリックします。
    モデルを編集するため、デザイン サーフェイスを提供する EF デザイナーが表示されます。

## <a name="map-the-person-entity-to-stored-procedures"></a>Person エンティティをストアド プロシージャにマップします。

-   右クリックし、 **Person**エンティティの種類と選択**ストアド プロシージャ マッピング**します。
-   ストアド プロシージャのマッピングが表示されます、**マッピングの詳細**ウィンドウ。
-   クリックして**&lt;関数の挿入 をクリック&gt;** します。
    このフィールドは、概念モデルのエンティティ型にマップできる、ストレージ モデル内のストアド プロシージャが表示されるドロップダウン リストです。
    選択**InsertPerson**ドロップダウン リストから。
-   ストアド プロシージャのパラメーターとエンティティのプロパティとの既定のマッピングが表示されます。 矢印はマッピングの方向を表します (プロパティの値がストアド プロシージャのパラメーターに渡される)。
-   クリックして**&lt;結果バインドの追加&gt;** します。
-   型**NewPersonID**、によって返されるパラメーターの名前、 **InsertPerson**ストアド プロシージャ。 先頭または末尾のスペースを入力することを確認してください。
-   **Enter** キーを押します。
-   既定では、 **NewPersonID**エンティティ キーにマップされて**PersonID**します。 矢印はマッピングの方向を表します (結果列の値がプロパティに渡される)。

    ![マッピングの詳細](~/ef6/media/mappingdetails.png)

-   をクリックして **&lt;Update 関数の選択&gt;** 選択**UpdatePerson**結果のドロップダウン リストから。
-   ストアド プロシージャのパラメーターとエンティティのプロパティとの既定のマッピングが表示されます。
-   をクリックして**&lt;削除関数の選択&gt;** 選択と**DeletePerson**結果のドロップダウン リストから。
-   ストアド プロシージャのパラメーターとエンティティのプロパティとの既定のマッピングが表示されます。

挿入、更新、および削除の操作、 **Person**エンティティ型がストアド プロシージャにマップされています。

同時実行更新またはストアド プロシージャを使用したエンティティの削除時のチェックを有効にする場合は、次のオプションのいずれかを使用します。

-   使用して、**出力**パラメーターの数を返すには、ストアド プロシージャとチェックからの行が影響を受ける、**行数パラメーター**パラメーター名の横にあるチェック ボックスをオンします。 操作が呼び出されると、返される値が 0 の場合、 [ **OptimisticConcurrencyException** ](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx)がスローされます。
-   チェック、**元の値を使用して**同時実行チェックに使用するプロパティの横にあるチェック ボックスをオンします。 更新しようとすると、元のデータベースにデータの書き込み時にデータベースから読み取られた元のプロパティの値が使用されます。 値が、データベース内の値と一致しない場合、 **OptimisticConcurrencyException**がスローされます。

## <a name="use-the-model"></a>モデルを使用します。

開く、 **Program.cs**ファイルの場所、 **Main**メソッドを定義します。 Main 関数には、次のコードを追加します。

コードを作成する新しい**Person**オブジェクト、オブジェクトを更新し、最後に、オブジェクトを削除します。         

``` csharp
    using (var context = new SchoolEntities())
    {
        var newInstructor = new Person
        {
            FirstName = "Robyn",
            LastName = "Martin",
            HireDate = DateTime.Now,
            Discriminator = "Instructor"
        }

        // Add the new object to the context.
        context.People.Add(newInstructor);

        Console.WriteLine("Added {0} {1} to the context.",
            newInstructor.FirstName, newInstructor.LastName);

        Console.WriteLine("Before SaveChanges, the PersonID is: {0}",
            newInstructor.PersonID);

        // SaveChanges will call the InsertPerson sproc.  
        // The PersonID property will be assigned the value
        // returned by the sproc.
        context.SaveChanges();

        Console.WriteLine("After SaveChanges, the PersonID is: {0}",
            newInstructor.PersonID);

        // Modify the object and call SaveChanges.
        // This time, the UpdatePerson will be called.
        newInstructor.FirstName = "Rachel";
        context.SaveChanges();

        // Remove the object from the context and call SaveChanges.
        // The DeletePerson sproc will be called.
        context.People.Remove(newInstructor);
        context.SaveChanges();

        Person deletedInstructor = context.People.
            Where(p => p.PersonID == newInstructor.PersonID).
            FirstOrDefault();

        if (deletedInstructor == null)
            Console.WriteLine("A person with PersonID {0} was deleted.",
                newInstructor.PersonID);
    }
```

-   アプリケーションをコンパイルして実行します。 プログラムには、次の出力が生成されます *。
    >[!NOTE]
> PersonID、サーバーによって自動生成されたため、別の数 * 表示ほとんどの場合には、します。

```
Added Robyn Martin to the context.
Before SaveChanges, the PersonID is: 0
After SaveChanges, the PersonID is: 51
A person with PersonID 51 was deleted.
```

Ultimate のバージョンの Visual Studio を使用する場合は、実行される SQL ステートメントを表示すると、デバッガーで Intellitrace を使用できます。

![Intellitrace](~/ef6/media/intellitrace.png)
