---
title: 複数の結果セットに EF6 を使用するストアド プロシージャ
author: divega
ms.date: 2016-10-23
ms.assetid: 1b3797f9-cd3d-4752-a55e-47b84b399dc1
ms.openlocfilehash: bb104ac5f584d26d279259a173de9afe3f018968
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996176"
---
# <a name="stored-procedures-with-multiple-result-sets"></a>複数の結果セットを使用したストアド プロシージャ
使用して格納されているときは 1 つ以上の結果を返す必要がありますプロシージャに設定します。 このシナリオはよくデータベースの数を減らす使用ラウンド トリップが 1 つの画面を作成するために必要です。 EF5 より前は、Entity Framework は、呼び出されるストアド プロシージャが、呼び出し元のコードに設定する最初の結果を返すだけです。

この記事では、Entity Framework でストアド プロシージャから 1 つ以上の結果セットへのアクセスに使用できる 2 つの方法を示します。 もう 1 つだけのコードを使用し、最初のコードの両方で動作と EF Designer EF Designer でのみ機能します。 ツールと API サポートする必要がありますを向上させる将来のバージョンの Entity Framework。

## <a name="model"></a>モデル

この記事の例では、基本的なブログを使用して、ブログが多くの投稿と、投稿がある投稿のモデルが 1 つのブログに属しています。 ここでは、ストアド プロシージャを返すすべてのブログや投稿を次のように、データベースで使います。

``` SQL
    CREATE PROCEDURE [dbo].[GetAllBlogsAndPosts]
    AS
        SELECT * FROM dbo.Blogs
        SELECT * FROM dbo.Posts
```

## <a name="accessing-multiple-result-sets-with-code"></a>コードを使用したセットの複数の結果にアクセスします。

ストアド プロシージャを実行する生の SQL コマンドを発行するコードを使用して実行できます。 このアプローチの利点は、最初にコードの両方で動作にして、EF デザイナー。

複数の結果を取得するために、IObjectContextAdapter インターフェイスを使用して、ObjectContext API を削除する必要があります。 作業を設定します。

Translate メソッドを使用してストアド プロシージャの結果を追跡して通常どおり、EF で使用できるエンティティに変換することができますし、ObjectContext を行っています。 次のコード サンプルでは、アクションでこれを示します。

``` csharp
    using (var db = new BloggingContext())
    {
        // If using Code First we need to make sure the model is built before we open the connection
        // This isn't required for models created with the EF Designer
        db.Database.Initialize(force: false);

        // Create a SQL command to execute the sproc
        var cmd = db.Database.Connection.CreateCommand();
        cmd.CommandText = "[dbo].[GetAllBlogsAndPosts]";

        try
        {

            db.Database.Connection.Open();
            // Run the sproc
            var reader = cmd.ExecuteReader();

            // Read Blogs from the first result set
            var blogs = ((IObjectContextAdapter)db)
                .ObjectContext
                .Translate<Blog>(reader, "Blogs", MergeOption.AppendOnly);   


            foreach (var item in blogs)
            {
                Console.WriteLine(item.Name);
            }        

            // Move to second result set and read Posts
            reader.NextResult();
            var posts = ((IObjectContextAdapter)db)
                .ObjectContext
                .Translate<Post>(reader, "Posts", MergeOption.AppendOnly);


            foreach (var item in posts)
            {
                Console.WriteLine(item.Title);
            }
        }
        finally
        {
            db.Database.Connection.Close();
        }
    }
```

Translate メソッドでは、プロシージャ、EntitySet 名、および、MergeOption を実行したときに受信したリーダーを受け取ります。 EntitySet 名は、派生コンテキストで DbSet プロパティと同じになります。 MergeOption 列挙型では、メモリ内で同じエンティティが既に存在する場合の結果の処理方法を制御します。

ここでコレクションを反復処理、ブログの締めくくり NextResult、上記のコードを指定します。 これは重要ですので、次の結果セットに移動する前に、最初の結果セットを使用する必要があります。

2 つの変換後のブログと Post のエンティティは、他のエンティティと同じ方法 EF によって追跡されるはし、変更または削除および標準として保存できるようにメソッドが呼び出されます。

>[!NOTE]
> EF では、変換メソッドを使用してエンティティの作成時にアカウントにすべてのマッピングは考慮されません。 結果のクラスのプロパティ名を持つセット内の列名には単に一致します。

>[!NOTE]
> それらすべてを既に読み込まれている場合でもを投稿をブログ エンティティのいずれかで投稿プロパティにアクセスする遅延読み込みを有効にした場合、EF はデータベースに接続すべてを遅延読み込みします。 これはため、EF は、すべての投稿が読み込まれるかどうかがあるかどうか、データベースの詳細を知ることはできません。 この問題を回避する場合は、遅延読み込みを無効にする必要があります。

## <a name="multiple-result-sets-with-configured-in-edmx"></a>複数の結果セットで構成された EDMX

>[!NOTE]
> EDMX の複数の結果セットを構成できる .NET Framework 4.5 を対象にする必要があります。 .NET 4.0 を対象としている場合は、前のセクションで示したコードに基づくメソッドを使用できます。

EF デザイナーを使用している場合で、返されるさまざまな結果セットの説明を認識できるように、モデルを変更することもできます。 前に、手の形は、ツールが複数の結果ではないことを知ることが 1 つは設定 edmx ファイルを手動で編集する必要がありますので注意してくださかった。 これは動作しますが、VS で、モデルの検証も中断されるように、edmx ファイルを編集します。 したがって、モデルを検証する場合は常にエラーが発生します。

-   これを行うには、1 つの結果セット クエリの場合と同様に、モデルにストアド プロシージャを追加する必要があります。
-   モデルを右クリックし、選択する必要があります**プログラムから開く.** **Xml**

    ![OpenAs](~/ef6/media/openas.png)

1 回、次の手順を行う必要があるし、XML として開かモデルがあります。

-   モデルの複合型および関数インポートを見つけます。

``` xml
    <!-- CSDL content -->
    <edmx:ConceptualModels>

    ...

      <FunctionImport Name="GetAllBlogsAndPosts" ReturnType="Collection(BlogModel.GetAllBlogsAndPosts_Result)" />

    ...

      <ComplexType Name="GetAllBlogsAndPosts_Result">
        <Property Type="Int32" Name="BlogId" Nullable="false" />
        <Property Type="String" Name="Name" Nullable="false" MaxLength="255" />
        <Property Type="String" Name="Description" Nullable="true" />
      </ComplexType>

    ...

    </edmx:ConceptualModels>
```

 

-   複合型を削除します。
-   今回の場合は、次のようになりますが、エンティティにマップされるように、関数インポートを更新します。

``` xml
    <FunctionImport Name="GetAllBlogsAndPosts">
      <ReturnType EntitySet="Blogs" Type="Collection(BlogModel.Blog)" />
      <ReturnType EntitySet="Posts" Type="Collection(BlogModel.Post)" />
    </FunctionImport>
```

これにより、ストアド プロシージャが、ブログ エントリの 1 つ、post エントリの 1 つの 2 つのコレクションを返すことのモデル。

-   関数マッピング要素を検索します。

``` xml
    <!-- C-S mapping content -->
    <edmx:Mappings>

    ...

      <FunctionImportMapping FunctionImportName="GetAllBlogsAndPosts" FunctionName="BlogModel.Store.GetAllBlogsAndPosts">
        <ResultMapping>
          <ComplexTypeMapping TypeName="BlogModel.GetAllBlogsAndPosts_Result">
            <ScalarProperty Name="BlogId" ColumnName="BlogId" />
            <ScalarProperty Name="Name" ColumnName="Name" />
            <ScalarProperty Name="Description" ColumnName="Description" />
          </ComplexTypeMapping>
        </ResultMapping>
      </FunctionImportMapping>

    ...

    </edmx:Mappings>
```

-   各エンティティが返された場合、次のいずれかの結果のマッピングを置き換えます。

``` xml
    <ResultMapping>
      <EntityTypeMapping TypeName ="BlogModel.Blog">
        <ScalarProperty Name="BlogId" ColumnName="BlogId" />
        <ScalarProperty Name="Name" ColumnName="Name" />
        <ScalarProperty Name="Description" ColumnName="Description" />
      </EntityTypeMapping>
    </ResultMapping>
    <ResultMapping>
      <EntityTypeMapping TypeName="BlogModel.Post">
        <ScalarProperty Name="BlogId" ColumnName="BlogId" />
        <ScalarProperty Name="PostId" ColumnName="PostId"/>
        <ScalarProperty Name="Title" ColumnName="Title" />
        <ScalarProperty Name="Text" ColumnName="Text" />
      </EntityTypeMapping>
    </ResultMapping>
```

既定で作成されたものなどの複合型に結果セットをマップすることもできます。 これを行うには、それを削除する代わりに、新しい複合型を作成し、複合型はすべての場所で上記の例でエンティティの名前を使用したことを使用します。

いったんモデルを保存して、ストアド プロシージャを使用する次のコードを実行し、これらのマッピングが変更されました。

``` csharp
    using (var db = new BlogEntities())
    {
        var results = db.GetAllBlogsAndPosts();

        foreach (var result in results)
        {
            Console.WriteLine("Blog: " + result.Name);
        }

        var posts = results.GetNextResult<Post>();

        foreach (var result in posts)
        {
            Console.WriteLine("Post: " + result.Title);
        }

        Console.ReadLine();
    }
```

>[!NOTE]
> モデルの edmx ファイルを手動で編集する場合は、これまで、データベースからモデルを再生成する場合は上書きされます。

## <a name="summary"></a>まとめ

ここで Entity Framework を使用して複数の結果へのアクセスの 2 つの方法の設定を示しました。 これらの両方に、状況に応じて変わらず有効ですが、設定して、状況に最適と思われる 1 つを選択する必要があります。 複数の結果セットがでのサポート向上バージョンの Entity Framework で将来的にし、このドキュメントの手順を実行しなくなるために必要な予定です。
