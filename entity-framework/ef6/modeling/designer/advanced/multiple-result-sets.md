---
title: 複数の結果セットを持つストアドプロシージャ-EF6
description: Entity Framework 6 で複数の結果セットを持つストアドプロシージャ
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/multiple-result-sets
ms.openlocfilehash: 6b213b944ca06a7ca141746d86f1127be2fd7d87
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070225"
---
# <a name="stored-procedures-with-multiple-result-sets"></a>複数の結果セットを持つストアドプロシージャ
ストアドプロシージャを使用する場合、複数の結果セットを返す必要がある場合があります。 このシナリオは、1つの画面を作成するために必要なデータベースラウンドトリップの数を減らすためによく使用されます。EF5 より前の Entity Framework では、ストアドプロシージャを呼び出すことができますが、呼び出し元のコードに返されるのは最初の結果セットだけです。

この記事では、を使用して Entity Framework のストアドプロシージャから複数の結果セットにアクセスする2つの方法について説明します。 1つはコードだけを使用し、Code first と EF デザイナーの両方で動作し、EF デザイナーでのみ動作するものです。 このためのツールと API のサポートは、今後のバージョンの Entity Framework で改善されます。

## <a name="model"></a>モデル

この記事の例では、ブログに多数の投稿があり、投稿が1つのブログに属している、基本的なブログと投稿モデルを使用しています。 次のように、すべてのブログと投稿を返すストアドプロシージャをデータベースに使用します。

``` SQL
    CREATE PROCEDURE [dbo].[GetAllBlogsAndPosts]
    AS
        SELECT * FROM dbo.Blogs
        SELECT * FROM dbo.Posts
```

## <a name="accessing-multiple-result-sets-with-code"></a>コードを使用した複数の結果セットへのアクセス

コードを実行して、ストアドプロシージャを実行するための未加工の SQL コマンドを発行できます。 この方法の利点は、Code first と EF Designer の両方で動作することです。

複数の結果セットを機能させるには、IObjectContextAdapter インターフェイスを使用して、ObjectContext API にドロップする必要があります。

ObjectContext が得られたら、Translate メソッドを使用して、ストアドプロシージャの結果を、EF で通常どおりに追跡および使用できるエンティティに変換できます。 次のコードサンプルは、実際の動作を示しています。

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

Translate メソッドは、プロシージャを実行したときに受け取ったリーダー、EntitySet 名、および MergeOption を受け入れます。 EntitySet 名は、派生コンテキストの DbSet プロパティと同じになります。 MergeOption 列挙型は、同じエンティティが既にメモリに存在する場合に結果を処理する方法を制御します。

ここでは、次の結果セットに移動する前に最初の結果セットを使用する必要があるため、NextResult を呼び出す前にブログのコレクションを反復処理しています。

2つの translate メソッドが呼び出されると、他のエンティティと同じ方法でブログおよび投稿エンティティが、EF によって追跡されます。そのため、変更または削除したり、通常どおりに保存したりすることができます。

>[!NOTE]
> EF では、Translate メソッドを使用してエンティティを作成するときに、マッピングは考慮されません。 結果セットの列名と、クラスのプロパティ名が一致するだけです。

>[!NOTE]
> 遅延読み込みが有効になっている場合、いずれかのブログエンティティの [投稿] プロパティにアクセスすると、EF はデータベースに接続し、すべての投稿を既に読み込んでいる場合でも、すべての投稿を遅延読み込みします。 これは、すべての投稿が読み込まれたかどうか、またはデータベースにさらにがあるかどうかを EF が認識できないためです。 これを回避するには、遅延読み込みを無効にする必要があります。

## <a name="multiple-result-sets-with-configured-in-edmx"></a>EDMX で構成された複数の結果セット

>[!NOTE]
> EDMX で複数の結果セットを構成できるようにするには、.NET Framework 4.5 をターゲットにする必要があります。 .NET 4.0 を対象としている場合は、前のセクションで示したコードベースのメソッドを使用できます。

EF デザイナーを使用している場合は、返されるさまざまな結果セットが認識されるようにモデルを変更することもできます。 前に知っておくべきことは、ツールが複数の結果セットに対応しているわけではないことです。そのため、edmx ファイルを手動で編集する必要があります。 このような edmx ファイルの編集は機能しますが、VS でモデルの検証も中断されます。 そのため、モデルを検証すると、常にエラーが発生します。

-   このためには、単一の結果セットクエリの場合と同じように、ストアドプロシージャをモデルに追加する必要があります。
-   これが完了したら、モデルを右クリックし、[**ファイルを開くアプリケーションの**選択] を選択します。 次に **Xml**

    ![名前を付けて開く](~/ef6/media/openas.png)

モデルを XML として開いたら、次の手順を実行する必要があります。

-   モデル内の複合型と関数インポートを検索します。

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
-   関数インポートを更新して、エンティティにマップされるようにします。この場合、次のようになります。

``` xml
    <FunctionImport Name="GetAllBlogsAndPosts">
      <ReturnType EntitySet="Blogs" Type="Collection(BlogModel.Blog)" />
      <ReturnType EntitySet="Posts" Type="Collection(BlogModel.Post)" />
    </FunctionImport>
```

これにより、ストアドプロシージャが2つのコレクション (ブログエントリの1つと投稿エントリの1つ) を返すことがモデルに通知されます。

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

-   結果のマッピングを、返される各エンティティの1つに置き換えます。次に例を示します。

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

また、既定で作成されたものなど、結果セットを複合型にマップすることもできます。 これを行うには、新しい複合型を削除する代わりに作成し、上記の例のエンティティ名を使用していたあらゆる場所で複合型を使用します。

これらのマッピングが変更されたら、モデルを保存し、次のコードを実行してストアドプロシージャを使用することができます。

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
> モデルの edmx ファイルを手動で編集した場合は、データベースからモデルを再生成すると上書きされます。

## <a name="summary"></a>まとめ

ここでは、Entity Framework を使用して複数の結果セットにアクセスする2つの異なる方法について説明しました。 どちらも、状況や好みに応じて同じように有効であり、状況に適したものを選択する必要があります。 Entity Framework の将来のバージョンでは、複数の結果セットのサポートが改善される予定です。このドキュメントの手順を実行する必要はなくなります。
