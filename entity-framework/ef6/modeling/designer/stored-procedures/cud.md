---
title: デザイナー CUD ストアドプロシージャ-EF6
description: Entity Framework 6 のデザイナー CUD ストアドプロシージャ
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/stored-procedures/cud
ms.openlocfilehash: fae795922980452cf062a62e3cbb47de49b49c74
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073215"
---
# <a name="designer-cud-stored-procedures"></a>デザイナー CUD ストアドプロシージャ

このステップバイステップチュートリアルでは \\ 、Entity Framework Designer (EF デザイナー) を使用して、エンティティ型の create insert、update、および delete (CUD) 操作をストアドプロシージャにマップする方法を示します。 既定では、CUD 操作の SQL ステートメントが Entity Framework によって自動的に生成されますが、ストアドプロシージャをこれらの操作にマップすることもできます。  

この Code First では、ストアドプロシージャまたは関数へのマッピングはサポートされていません。 ただし、ストアドプロシージャまたは関数は、ストアドプロシージャまたは関数を呼び出すことによって呼び出すことができます。 次に例を示します。

``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]");
```

## <a name="considerations-when-mapping-the-cud-operations-to-stored-procedures"></a>CUD 操作をストアドプロシージャにマップする際の注意点

CUD 操作をストアドプロシージャにマップする場合は、次の考慮事項が適用されます。

- CUD 操作のいずれかをストアドプロシージャにマップする場合は、すべての操作をマップします。 これら3つをすべてマップしないと、マップされていない操作が実行され、 **Updateexception**   がスローされた場合に失敗します。
- ストアドプロシージャのすべてのパラメーターをエンティティプロパティにマップする必要があります。
- 挿入された行の主キーの値をサーバーが生成する場合は、この値をエンティティのキープロパティにマップする必要があります。 次の例では、 **Insertperson**   ストアドプロシージャは、新しく作成された主キーをストアドプロシージャの結果セットの一部として返します。 プライマリキーは、EF デザイナーの [ ** &lt; 結果 &gt; バインドの追加**] 機能を使用してエンティティキー (**PersonID**) にマップされ   ます。
- ストアドプロシージャの呼び出しは、概念モデルのエンティティを使用して1:1 にマップされます。 たとえば、概念モデルに継承階層を実装し、 **親** (基本) エンティティと **子** (派生) エンティティに対して CUD ストアドプロシージャをマップした場合、 **子** の変更を保存しても、 **子**のストアドプロシージャのみが呼び出されます。この場合、 **親**のストアドプロシージャの呼び出しはトリガーされません。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するための要件を次に示します。

- Visual Studio の最新バージョン。
- [School サンプルデータベース](xref:ef6/resources/school-database)。

## <a name="set-up-the-project"></a>プロジェクトを設定する

- Visual Studio 2012 を開きます。
- **ファイルの選択- &gt; 新規作成- &gt; プロジェクト**
- 左側のウィンドウで、[ **Visual C \# **] をクリックし、**コンソール**テンプレートを選択します。
- 名前として「 **Cudsprocssample**」と入力し   ます。
-  **[OK]** を選択します。

## <a name="create-a-model"></a>モデルの作成

- ソリューションエクスプローラーでプロジェクト名を右クリックし、[追加]、[ ** &gt; 新しい項目**] の順に選択します。
- 左側のメニューから [ **データ** ] を選択し、[テンプレート] ペインで [ **ADO.NET Entity Data Model** ] を選択します。
- ファイル名として「 **Cudsprocs .edmx** 」と入力し、[ **追加**] をクリックします。
- [モデルのコンテンツの選択] ダイアログボックスで、[ **データベースから生成**] を選択し、[ **次へ**] をクリックします。
- [ **新しい接続**] をクリックします。 [接続プロパティ] ダイアログボックスで、サーバー名 (たとえば、 **(localdb) \\ mssqllocaldb**) を入力し、認証方法を選択します。データベース名として「 **School**」と入力し、[    **OK]** をクリックします。
    [データ接続の選択] ダイアログボックスが、データベース接続の設定によって更新されます。
- [データベースオブジェクトの選択] ダイアログボックスの [ **テーブル**] ノードで、[    **Person** ] テーブルを選択します。
- また、[ **ストアドプロシージャと関数** ] ノードで、[ **deleteperson**]、[ **Insertperson**]、[ **updateperson**] の各ストアドプロシージャを選択します。
- Visual Studio 2012 以降では、EF デザイナーはストアドプロシージャの一括インポートをサポートしています。 既定では、[ **選択したストアドプロシージャと関数をエンティティモデルにインポート** する] がオンになっています。 この例では、エンティティ型の挿入、更新、および削除を行うストアドプロシージャがあります。これをインポートするのではなく、このチェックボックスをオフにします。

    ![プロシージャのインポート](~/ef6/media/importsprocs.jpg)

- [ **完了**] をクリックします。
    モデルを編集するためのデザイン画面を提供する EF デザイナーが表示されます。

## <a name="map-the-person-entity-to-stored-procedures"></a>Person エンティティをストアドプロシージャにマップする

-  **Person**エンティティ型を右クリックし、[    **ストアドプロシージャマッピング**] を選択します。
- [ **マッピングの詳細**] ウィンドウに、ストアドプロシージャのマッピングが表示され   ます。
- [ ** &lt; Insert 関数 &gt; の選択] を**クリックします。
    このフィールドは、概念モデルのエンティティ型にマップできる、ストレージ モデル内のストアド プロシージャが表示されるドロップダウン リストです。
    ドロップダウンリストから [ **Insertperson**] を選択し   ます。
- ストアド プロシージャのパラメーターとエンティティのプロパティとの既定のマッピングが表示されます。 矢印はマッピングの方向を表します (プロパティの値がストアド プロシージャのパラメーターに渡される)。
- [ ** &lt; 結果バインド &gt; の追加**] をクリックします。
-  **Insertperson**ストアドプロシージャによって返されるパラメーターの名前 **NewPersonID**を入力し   ます。 先頭または末尾にスペースを入力しないようにしてください。
- Enter **キーを押します**。
- 既定では、 **NewPersonID**   はエンティティキー **PersonID**にマップされます。 矢印はマッピングの方向を表します (結果列の値がプロパティに渡される)。

    ![マッピングの詳細](~/ef6/media/mappingdetails.png)

- [ ** &lt; Update &gt; 関数の選択**] をクリックし   、結果のドロップダウンリストから [ **updateperson**] を選択し   ます。
- ストアド プロシージャのパラメーターとエンティティのプロパティとの既定のマッピングが表示されます。
- [ ** &lt; 削除 &gt; 関数の選択**] をクリックし   、結果として表示されるドロップダウンリストから [ **deleteperson**] を選択し   ます。
- ストアド プロシージャのパラメーターとエンティティのプロパティとの既定のマッピングが表示されます。

 **Person**エンティティ型の挿入、更新、および削除の各操作   がストアドプロシージャにマップされるようになりました。

ストアドプロシージャを使用してエンティティを更新または削除するときに、同時実行チェックを有効にする場合は、次のいずれかのオプションを使用します。

- **OUTPUT**パラメーターを使用して、ストアドプロシージャから影響を受ける行の数を返し、パラメーター名の横にある [**影響を受けた行のパラメーター**   ] チェックボックスをオンにします。 操作が呼び出されたときに返される値が0の場合、  [**OptimisticConcurrencyException**](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx)が   スローされます。
- 同時実行チェックに使用するプロパティの横にある [ **元の値を使用** する] チェックボックスをオンにします。 更新が試行されると、データベースにデータを書き戻すときに、最初にデータベースから読み取られたプロパティの値が使用されます。 値がデータベースの値と一致しない場合は、 **OptimisticConcurrencyException**   がスローされます。

## <a name="use-the-model"></a>モデルを使用する

**Main**メソッドが定義されている**Program.cs**ファイルを開きます。 Main 関数に次のコードを追加します。

このコードは、新しい **Person** オブジェクトを作成し、オブジェクトを更新して、最後にオブジェクトを削除します。

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

- アプリケーションをコンパイルして実行します。 プログラムは次の出力を生成します *

> [!NOTE]
> PersonID はサーバーによって自動生成されるので、ほとんどの場合は異なる数 * が表示されます。

``` Output
Added Robyn Martin to the context.
Before SaveChanges, the PersonID is: 0
After SaveChanges, the PersonID is: 51
A person with PersonID 51 was deleted.
```

Ultimate バージョンの Visual Studio を使用している場合は、デバッガーで Intellitrace を使用して、実行される SQL ステートメントを確認できます。

![Intellitrace を使用したデバッグ](~/ef6/media/intellitrace.png)
