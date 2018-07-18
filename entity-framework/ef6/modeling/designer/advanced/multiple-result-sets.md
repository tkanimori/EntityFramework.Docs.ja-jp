---
title: 複数の結果セットに EF6 を使用するストアド プロシージャ
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 1b3797f9-cd3d-4752-a55e-47b84b399dc1
caps.latest.revision: 3
ms.openlocfilehash: 68d544b0c553868ad1ff36cd24db19cff08db073
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2018
ms.locfileid: "39122388"
---
# <a name="stored-procedures-with-multiple-result-sets"></a><span data-ttu-id="bf48b-102">複数の結果セットを使用したストアド プロシージャ</span><span class="sxs-lookup"><span data-stu-id="bf48b-102">Stored Procedures with Multiple Result Sets</span></span>
<span data-ttu-id="bf48b-103">使用して格納されているときは 1 つ以上の結果を返す必要がありますプロシージャに設定します。</span><span class="sxs-lookup"><span data-stu-id="bf48b-103">Sometimes when using stored procedures you will need to return more than one result set.</span></span> <span data-ttu-id="bf48b-104">このシナリオはよくデータベースの数を減らす使用ラウンド トリップが 1 つの画面を作成するために必要です。</span><span class="sxs-lookup"><span data-stu-id="bf48b-104">This scenario is commonly used to reduce the number of database round trips required to compose a single screen.</span></span> <span data-ttu-id="bf48b-105">EF5 より前は、Entity Framework は、呼び出されるストアド プロシージャが、呼び出し元のコードに設定する最初の結果を返すだけです。</span><span class="sxs-lookup"><span data-stu-id="bf48b-105">Prior to EF5, Entity Framework would allow the stored procedure to be called but would only return the first result set to the calling code.</span></span>

<span data-ttu-id="bf48b-106">この記事では、Entity Framework でストアド プロシージャから 1 つ以上の結果セットへのアクセスに使用できる 2 つの方法を示します。</span><span class="sxs-lookup"><span data-stu-id="bf48b-106">This article will show you two ways that you can use to access more than one result set from a stored procedure in Entity Framework.</span></span> <span data-ttu-id="bf48b-107">もう 1 つだけのコードを使用し、最初のコードの両方で動作と EF Designer EF Designer でのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="bf48b-107">One that uses just code and works with both Code first and the EF Designer and one that only works with the EF Designer.</span></span> <span data-ttu-id="bf48b-108">ツールと API サポートする必要がありますを向上させる将来のバージョンの Entity Framework。</span><span class="sxs-lookup"><span data-stu-id="bf48b-108">The tooling and API support for this should improve in future versions of Entity Framework.</span></span>

## <a name="model"></a><span data-ttu-id="bf48b-109">モデル</span><span class="sxs-lookup"><span data-stu-id="bf48b-109">Model</span></span>

<span data-ttu-id="bf48b-110">この記事の例では、基本的なブログを使用して、ブログが多くの投稿と、投稿がある投稿のモデルが 1 つのブログに属しています。</span><span class="sxs-lookup"><span data-stu-id="bf48b-110">The examples in this article use a basic Blog and Posts model where a blog has many posts and a post belongs to a single blog.</span></span> <span data-ttu-id="bf48b-111">ここでは、ストアド プロシージャを返すすべてのブログや投稿を次のように、データベースで使います。</span><span class="sxs-lookup"><span data-stu-id="bf48b-111">We will use a stored procedure in the database that returns all blogs and posts, something like this:</span></span>

``` SQL
    CREATE PROCEDURE [dbo].[GetAllBlogsAndPosts]
    AS
        SELECT * FROM dbo.Blogs
        SELECT * FROM dbo.Posts
```

## <a name="accessing-multiple-result-sets-with-code"></a><span data-ttu-id="bf48b-112">コードを使用したセットの複数の結果にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="bf48b-112">Accessing Multiple Result Sets with Code</span></span>

<span data-ttu-id="bf48b-113">ストアド プロシージャを実行する生の SQL コマンドを発行するコードを使用して実行できます。</span><span class="sxs-lookup"><span data-stu-id="bf48b-113">We can execute use code to issue a raw SQL command to execute our stored procedure.</span></span> <span data-ttu-id="bf48b-114">このアプローチの利点は、最初にコードの両方で動作にして、EF デザイナー。</span><span class="sxs-lookup"><span data-stu-id="bf48b-114">The advantage of this approach is that it works with both Code first and the EF Designer.</span></span>

<span data-ttu-id="bf48b-115">複数の結果を取得するために、IObjectContextAdapter インターフェイスを使用して、ObjectContext API を削除する必要があります。 作業を設定します。</span><span class="sxs-lookup"><span data-stu-id="bf48b-115">In order to get multiple result sets working we need to drop to the ObjectContext API by using the IObjectContextAdapter interface.</span></span>

<span data-ttu-id="bf48b-116">Translate メソッドを使用してストアド プロシージャの結果を追跡して通常どおり、EF で使用できるエンティティに変換することができますし、ObjectContext を行っています。</span><span class="sxs-lookup"><span data-stu-id="bf48b-116">Once we have an ObjectContext then we can use the Translate method to translate the results of our stored procedure into entities that can be tracked and used in EF as normal.</span></span> <span data-ttu-id="bf48b-117">次のコード サンプルでは、アクションでこれを示します。</span><span class="sxs-lookup"><span data-stu-id="bf48b-117">The following code sample demonstrates this in action.</span></span>

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

<span data-ttu-id="bf48b-118">Translate メソッドでは、プロシージャ、EntitySet 名、および、MergeOption を実行したときに受信したリーダーを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="bf48b-118">The Translate method accepts the reader that we received when we executed the procedure, an EntitySet name, and a MergeOption.</span></span> <span data-ttu-id="bf48b-119">EntitySet 名は、派生コンテキストで DbSet プロパティと同じになります。</span><span class="sxs-lookup"><span data-stu-id="bf48b-119">The EntitySet name will be the same as the DbSet property on your derived context.</span></span> <span data-ttu-id="bf48b-120">MergeOption 列挙型では、メモリ内で同じエンティティが既に存在する場合の結果の処理方法を制御します。</span><span class="sxs-lookup"><span data-stu-id="bf48b-120">The MergeOption enum controls how results are handled if the same entity already exists in memory.</span></span>

<span data-ttu-id="bf48b-121">ここでコレクションを反復処理、ブログの締めくくり NextResult、上記のコードを指定します。 これは重要ですので、次の結果セットに移動する前に、最初の結果セットを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bf48b-121">Here we iterate through the collection of blogs before we call NextResult, this is important given the above code because the first result set must be consumed before moving to the next result set.</span></span>

<span data-ttu-id="bf48b-122">2 つの変換後のブログと Post のエンティティは、他のエンティティと同じ方法 EF によって追跡されるはし、変更または削除および標準として保存できるようにメソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="bf48b-122">Once the two translate methods are called then the Blog and Post entities are tracked by EF the same way as any other entity and so can be modified or deleted and saved as normal.</span></span>

>[!NOTE]
> <span data-ttu-id="bf48b-123">EF では、変換メソッドを使用してエンティティの作成時にアカウントにすべてのマッピングは考慮されません。</span><span class="sxs-lookup"><span data-stu-id="bf48b-123">EF does not take any mapping into account when it creates entities using the Translate method.</span></span> <span data-ttu-id="bf48b-124">結果のクラスのプロパティ名を持つセット内の列名には単に一致します。</span><span class="sxs-lookup"><span data-stu-id="bf48b-124">It will simply match column names in the result set with property names on your classes.</span></span>

>[!NOTE]
> <span data-ttu-id="bf48b-125">それらすべてを既に読み込まれている場合でもを投稿をブログ エンティティのいずれかで投稿プロパティにアクセスする遅延読み込みを有効にした場合、EF はデータベースに接続すべてを遅延読み込みします。</span><span class="sxs-lookup"><span data-stu-id="bf48b-125">That if you have lazy loading enabled, accessing the posts property on one of the blog entities then EF will connect to the database to lazily load all posts, even though we have already loaded them all.</span></span> <span data-ttu-id="bf48b-126">これはため、EF は、すべての投稿が読み込まれるかどうかがあるかどうか、データベースの詳細を知ることはできません。</span><span class="sxs-lookup"><span data-stu-id="bf48b-126">This is because EF cannot know whether or not you have loaded all posts or if there are more in the database.</span></span> <span data-ttu-id="bf48b-127">この問題を回避する場合は、遅延読み込みを無効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="bf48b-127">If you want to avoid this then you will need to disable lazy loading.</span></span>

## <a name="multiple-result-sets-with-configured-in-edmx"></a><span data-ttu-id="bf48b-128">複数の結果セットで構成された EDMX</span><span class="sxs-lookup"><span data-stu-id="bf48b-128">Multiple Result Sets with Configured in EDMX</span></span>

>[!NOTE]
> <span data-ttu-id="bf48b-129">EDMX の複数の結果セットを構成できる .NET Framework 4.5 を対象にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="bf48b-129">You must target .NET Framework 4.5 to be able to configure multiple result sets in EDMX.</span></span> <span data-ttu-id="bf48b-130">.NET 4.0 を対象としている場合は、前のセクションで示したコードに基づくメソッドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="bf48b-130">If you are targeting .NET 4.0 you can use the code-based method shown in the previous section.</span></span>

<span data-ttu-id="bf48b-131">EF デザイナーを使用している場合で、返されるさまざまな結果セットの説明を認識できるように、モデルを変更することもできます。</span><span class="sxs-lookup"><span data-stu-id="bf48b-131">If you are using the EF Designer, you can also modify your model so that it knows about the different result sets that will be returned.</span></span> <span data-ttu-id="bf48b-132">前に、手の形は、ツールが複数の結果ではないことを知ることが 1 つは設定 edmx ファイルを手動で編集する必要がありますので注意してくださかった。</span><span class="sxs-lookup"><span data-stu-id="bf48b-132">One thing to know before hand is that the tooling is not multiple result set aware, so you will need to manually edit the edmx file.</span></span> <span data-ttu-id="bf48b-133">これは動作しますが、VS で、モデルの検証も中断されるように、edmx ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="bf48b-133">Editing the edmx file like this will work but it will also break the validation of the model in VS.</span></span> <span data-ttu-id="bf48b-134">したがって、モデルを検証する場合は常にエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="bf48b-134">So if you validate your model you will always get errors.</span></span>

-   <span data-ttu-id="bf48b-135">これを行うには、1 つの結果セット クエリの場合と同様に、モデルにストアド プロシージャを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bf48b-135">In order to do this you need to add the stored procedure to your model as you would for a single result set query.</span></span>
-   <span data-ttu-id="bf48b-136">モデルを右クリックし、選択する必要があります**プログラムから開く.**</span><span class="sxs-lookup"><span data-stu-id="bf48b-136">Once you have this then you need to right click on your model and select **Open With..**</span></span> <span data-ttu-id="bf48b-137">**Xml**</span><span class="sxs-lookup"><span data-stu-id="bf48b-137">then **Xml**</span></span>

    ![OpenAs](~/ef6/media/openas.png)

<span data-ttu-id="bf48b-139">1 回、次の手順を行う必要があるし、XML として開かモデルがあります。</span><span class="sxs-lookup"><span data-stu-id="bf48b-139">Once you have the model opened as XML then you need to do the following steps:</span></span>

-   <span data-ttu-id="bf48b-140">モデルの複合型および関数インポートを見つけます。</span><span class="sxs-lookup"><span data-stu-id="bf48b-140">Find the complex type and function import in your model:</span></span>

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

 

-   <span data-ttu-id="bf48b-141">複合型を削除します。</span><span class="sxs-lookup"><span data-stu-id="bf48b-141">Remove the complex type</span></span>
-   <span data-ttu-id="bf48b-142">今回の場合は、次のようになりますが、エンティティにマップされるように、関数インポートを更新します。</span><span class="sxs-lookup"><span data-stu-id="bf48b-142">Update the function import so that it maps to your entities, in our case it will look like the following:</span></span>

``` xml
    <FunctionImport Name="GetAllBlogsAndPosts">
      <ReturnType EntitySet="Blogs" Type="Collection(BlogModel.Blog)" />
      <ReturnType EntitySet="Posts" Type="Collection(BlogModel.Post)" />
    </FunctionImport>
```

<span data-ttu-id="bf48b-143">これにより、ストアド プロシージャが、ブログ エントリの 1 つ、post エントリの 1 つの 2 つのコレクションを返すことのモデル。</span><span class="sxs-lookup"><span data-stu-id="bf48b-143">This tells the model that the stored procedure will return two collections, one of blog entries and one of post entries.</span></span>

-   <span data-ttu-id="bf48b-144">関数マッピング要素を検索します。</span><span class="sxs-lookup"><span data-stu-id="bf48b-144">Find the function mapping element:</span></span>

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

-   <span data-ttu-id="bf48b-145">各エンティティが返された場合、次のいずれかの結果のマッピングを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="bf48b-145">Replace the result mapping with one for each entity being returned, such as the following:</span></span>

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

<span data-ttu-id="bf48b-146">既定で作成されたものなどの複合型に結果セットをマップすることもできます。</span><span class="sxs-lookup"><span data-stu-id="bf48b-146">It is also possible to map the result sets to complex types, such as the one created by default.</span></span> <span data-ttu-id="bf48b-147">これを行うには、それを削除する代わりに、新しい複合型を作成し、複合型はすべての場所で上記の例でエンティティの名前を使用したことを使用します。</span><span class="sxs-lookup"><span data-stu-id="bf48b-147">To do this you create a new complex type, instead of removing them, and use the complex types everywhere that you had used the entity names in the examples above.</span></span>

<span data-ttu-id="bf48b-148">いったんモデルを保存して、ストアド プロシージャを使用する次のコードを実行し、これらのマッピングが変更されました。</span><span class="sxs-lookup"><span data-stu-id="bf48b-148">Once these mappings have been changed then you can save the model and execute the following code to use the stored procedure:</span></span>

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
> <span data-ttu-id="bf48b-149">モデルの edmx ファイルを手動で編集する場合は、これまで、データベースからモデルを再生成する場合は上書きされます。</span><span class="sxs-lookup"><span data-stu-id="bf48b-149">If you manually edit the edmx file for your model it will be overwritten if you ever regenerate the model from the database.</span></span>

## <a name="summary"></a><span data-ttu-id="bf48b-150">まとめ</span><span class="sxs-lookup"><span data-stu-id="bf48b-150">Summary</span></span>

<span data-ttu-id="bf48b-151">ここで Entity Framework を使用して複数の結果へのアクセスの 2 つの方法の設定を示しました。</span><span class="sxs-lookup"><span data-stu-id="bf48b-151">Here we have shown two different methods of accessing multiple result sets using Entity Framework.</span></span> <span data-ttu-id="bf48b-152">これらの両方に、状況に応じて変わらず有効ですが、設定して、状況に最適と思われる 1 つを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bf48b-152">Both of them are equally valid depending on your situation and preferences and you should choose the one that seems best for your circumstances.</span></span> <span data-ttu-id="bf48b-153">複数の結果セットがでのサポート向上バージョンの Entity Framework で将来的にし、このドキュメントの手順を実行しなくなるために必要な予定です。</span><span class="sxs-lookup"><span data-stu-id="bf48b-153">It is planned that the support for multiple result sets will be improved in future versions of Entity Framework and that performing the steps in this document will no longer be necessary.</span></span>
