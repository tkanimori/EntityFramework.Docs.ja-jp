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
# <a name="stored-procedures-with-multiple-result-sets"></a><span data-ttu-id="627de-103">複数の結果セットを持つストアドプロシージャ</span><span class="sxs-lookup"><span data-stu-id="627de-103">Stored Procedures with Multiple Result Sets</span></span>
<span data-ttu-id="627de-104">ストアドプロシージャを使用する場合、複数の結果セットを返す必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="627de-104">Sometimes when using stored procedures you will need to return more than one result set.</span></span> <span data-ttu-id="627de-105">このシナリオは、1つの画面を作成するために必要なデータベースラウンドトリップの数を減らすためによく使用されます。</span><span class="sxs-lookup"><span data-stu-id="627de-105">This scenario is commonly used to reduce the number of database round trips required to compose a single screen.</span></span><span data-ttu-id="627de-106">EF5 より前の Entity Framework では、ストアドプロシージャを呼び出すことができますが、呼び出し元のコードに返されるのは最初の結果セットだけです。</span><span class="sxs-lookup"><span data-stu-id="627de-106"> Prior to EF5, Entity Framework would allow the stored procedure to be called but would only return the first result set to the calling code.</span></span>

<span data-ttu-id="627de-107">この記事では、を使用して Entity Framework のストアドプロシージャから複数の結果セットにアクセスする2つの方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="627de-107">This article will show you two ways that you can use to access more than one result set from a stored procedure in Entity Framework.</span></span> <span data-ttu-id="627de-108">1つはコードだけを使用し、Code first と EF デザイナーの両方で動作し、EF デザイナーでのみ動作するものです。</span><span class="sxs-lookup"><span data-stu-id="627de-108">One that uses just code and works with both Code first and the EF Designer and one that only works with the EF Designer.</span></span> <span data-ttu-id="627de-109">このためのツールと API のサポートは、今後のバージョンの Entity Framework で改善されます。</span><span class="sxs-lookup"><span data-stu-id="627de-109">The tooling and API support for this should improve in future versions of Entity Framework.</span></span>

## <a name="model"></a><span data-ttu-id="627de-110">モデル</span><span class="sxs-lookup"><span data-stu-id="627de-110">Model</span></span>

<span data-ttu-id="627de-111">この記事の例では、ブログに多数の投稿があり、投稿が1つのブログに属している、基本的なブログと投稿モデルを使用しています。</span><span class="sxs-lookup"><span data-stu-id="627de-111">The examples in this article use a basic Blog and Posts model where a blog has many posts and a post belongs to a single blog.</span></span> <span data-ttu-id="627de-112">次のように、すべてのブログと投稿を返すストアドプロシージャをデータベースに使用します。</span><span class="sxs-lookup"><span data-stu-id="627de-112">We will use a stored procedure in the database that returns all blogs and posts, something like this:</span></span>

``` SQL
    CREATE PROCEDURE [dbo].[GetAllBlogsAndPosts]
    AS
        SELECT * FROM dbo.Blogs
        SELECT * FROM dbo.Posts
```

## <a name="accessing-multiple-result-sets-with-code"></a><span data-ttu-id="627de-113">コードを使用した複数の結果セットへのアクセス</span><span class="sxs-lookup"><span data-stu-id="627de-113">Accessing Multiple Result Sets with Code</span></span>

<span data-ttu-id="627de-114">コードを実行して、ストアドプロシージャを実行するための未加工の SQL コマンドを発行できます。</span><span class="sxs-lookup"><span data-stu-id="627de-114">We can execute use code to issue a raw SQL command to execute our stored procedure.</span></span> <span data-ttu-id="627de-115">この方法の利点は、Code first と EF Designer の両方で動作することです。</span><span class="sxs-lookup"><span data-stu-id="627de-115">The advantage of this approach is that it works with both Code first and the EF Designer.</span></span>

<span data-ttu-id="627de-116">複数の結果セットを機能させるには、IObjectContextAdapter インターフェイスを使用して、ObjectContext API にドロップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="627de-116">In order to get multiple result sets working we need to drop to the ObjectContext API by using the IObjectContextAdapter interface.</span></span>

<span data-ttu-id="627de-117">ObjectContext が得られたら、Translate メソッドを使用して、ストアドプロシージャの結果を、EF で通常どおりに追跡および使用できるエンティティに変換できます。</span><span class="sxs-lookup"><span data-stu-id="627de-117">Once we have an ObjectContext then we can use the Translate method to translate the results of our stored procedure into entities that can be tracked and used in EF as normal.</span></span> <span data-ttu-id="627de-118">次のコードサンプルは、実際の動作を示しています。</span><span class="sxs-lookup"><span data-stu-id="627de-118">The following code sample demonstrates this in action.</span></span>

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

<span data-ttu-id="627de-119">Translate メソッドは、プロシージャを実行したときに受け取ったリーダー、EntitySet 名、および MergeOption を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="627de-119">The Translate method accepts the reader that we received when we executed the procedure, an EntitySet name, and a MergeOption.</span></span> <span data-ttu-id="627de-120">EntitySet 名は、派生コンテキストの DbSet プロパティと同じになります。</span><span class="sxs-lookup"><span data-stu-id="627de-120">The EntitySet name will be the same as the DbSet property on your derived context.</span></span> <span data-ttu-id="627de-121">MergeOption 列挙型は、同じエンティティが既にメモリに存在する場合に結果を処理する方法を制御します。</span><span class="sxs-lookup"><span data-stu-id="627de-121">The MergeOption enum controls how results are handled if the same entity already exists in memory.</span></span>

<span data-ttu-id="627de-122">ここでは、次の結果セットに移動する前に最初の結果セットを使用する必要があるため、NextResult を呼び出す前にブログのコレクションを反復処理しています。</span><span class="sxs-lookup"><span data-stu-id="627de-122">Here we iterate through the collection of blogs before we call NextResult, this is important given the above code because the first result set must be consumed before moving to the next result set.</span></span>

<span data-ttu-id="627de-123">2つの translate メソッドが呼び出されると、他のエンティティと同じ方法でブログおよび投稿エンティティが、EF によって追跡されます。そのため、変更または削除したり、通常どおりに保存したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="627de-123">Once the two translate methods are called then the Blog and Post entities are tracked by EF the same way as any other entity and so can be modified or deleted and saved as normal.</span></span>

>[!NOTE]
> <span data-ttu-id="627de-124">EF では、Translate メソッドを使用してエンティティを作成するときに、マッピングは考慮されません。</span><span class="sxs-lookup"><span data-stu-id="627de-124">EF does not take any mapping into account when it creates entities using the Translate method.</span></span> <span data-ttu-id="627de-125">結果セットの列名と、クラスのプロパティ名が一致するだけです。</span><span class="sxs-lookup"><span data-stu-id="627de-125">It will simply match column names in the result set with property names on your classes.</span></span>

>[!NOTE]
> <span data-ttu-id="627de-126">遅延読み込みが有効になっている場合、いずれかのブログエンティティの [投稿] プロパティにアクセスすると、EF はデータベースに接続し、すべての投稿を既に読み込んでいる場合でも、すべての投稿を遅延読み込みします。</span><span class="sxs-lookup"><span data-stu-id="627de-126">That if you have lazy loading enabled, accessing the posts property on one of the blog entities then EF will connect to the database to lazily load all posts, even though we have already loaded them all.</span></span> <span data-ttu-id="627de-127">これは、すべての投稿が読み込まれたかどうか、またはデータベースにさらにがあるかどうかを EF が認識できないためです。</span><span class="sxs-lookup"><span data-stu-id="627de-127">This is because EF cannot know whether or not you have loaded all posts or if there are more in the database.</span></span> <span data-ttu-id="627de-128">これを回避するには、遅延読み込みを無効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="627de-128">If you want to avoid this then you will need to disable lazy loading.</span></span>

## <a name="multiple-result-sets-with-configured-in-edmx"></a><span data-ttu-id="627de-129">EDMX で構成された複数の結果セット</span><span class="sxs-lookup"><span data-stu-id="627de-129">Multiple Result Sets with Configured in EDMX</span></span>

>[!NOTE]
> <span data-ttu-id="627de-130">EDMX で複数の結果セットを構成できるようにするには、.NET Framework 4.5 をターゲットにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="627de-130">You must target .NET Framework 4.5 to be able to configure multiple result sets in EDMX.</span></span> <span data-ttu-id="627de-131">.NET 4.0 を対象としている場合は、前のセクションで示したコードベースのメソッドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="627de-131">If you are targeting .NET 4.0 you can use the code-based method shown in the previous section.</span></span>

<span data-ttu-id="627de-132">EF デザイナーを使用している場合は、返されるさまざまな結果セットが認識されるようにモデルを変更することもできます。</span><span class="sxs-lookup"><span data-stu-id="627de-132">If you are using the EF Designer, you can also modify your model so that it knows about the different result sets that will be returned.</span></span> <span data-ttu-id="627de-133">前に知っておくべきことは、ツールが複数の結果セットに対応しているわけではないことです。そのため、edmx ファイルを手動で編集する必要があります。</span><span class="sxs-lookup"><span data-stu-id="627de-133">One thing to know before hand is that the tooling is not multiple result set aware, so you will need to manually edit the edmx file.</span></span> <span data-ttu-id="627de-134">このような edmx ファイルの編集は機能しますが、VS でモデルの検証も中断されます。</span><span class="sxs-lookup"><span data-stu-id="627de-134">Editing the edmx file like this will work but it will also break the validation of the model in VS.</span></span> <span data-ttu-id="627de-135">そのため、モデルを検証すると、常にエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="627de-135">So if you validate your model you will always get errors.</span></span>

-   <span data-ttu-id="627de-136">このためには、単一の結果セットクエリの場合と同じように、ストアドプロシージャをモデルに追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="627de-136">In order to do this you need to add the stored procedure to your model as you would for a single result set query.</span></span>
-   <span data-ttu-id="627de-137">これが完了したら、モデルを右クリックし、[**ファイルを開くアプリケーションの**選択] を選択します。</span><span class="sxs-lookup"><span data-stu-id="627de-137">Once you have this then you need to right click on your model and select **Open With..**</span></span> <span data-ttu-id="627de-138">次に **Xml**</span><span class="sxs-lookup"><span data-stu-id="627de-138">then **Xml**</span></span>

    ![名前を付けて開く](~/ef6/media/openas.png)

<span data-ttu-id="627de-140">モデルを XML として開いたら、次の手順を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="627de-140">Once you have the model opened as XML then you need to do the following steps:</span></span>

-   <span data-ttu-id="627de-141">モデル内の複合型と関数インポートを検索します。</span><span class="sxs-lookup"><span data-stu-id="627de-141">Find the complex type and function import in your model:</span></span>

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

 

-   <span data-ttu-id="627de-142">複合型を削除します。</span><span class="sxs-lookup"><span data-stu-id="627de-142">Remove the complex type</span></span>
-   <span data-ttu-id="627de-143">関数インポートを更新して、エンティティにマップされるようにします。この場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="627de-143">Update the function import so that it maps to your entities, in our case it will look like the following:</span></span>

``` xml
    <FunctionImport Name="GetAllBlogsAndPosts">
      <ReturnType EntitySet="Blogs" Type="Collection(BlogModel.Blog)" />
      <ReturnType EntitySet="Posts" Type="Collection(BlogModel.Post)" />
    </FunctionImport>
```

<span data-ttu-id="627de-144">これにより、ストアドプロシージャが2つのコレクション (ブログエントリの1つと投稿エントリの1つ) を返すことがモデルに通知されます。</span><span class="sxs-lookup"><span data-stu-id="627de-144">This tells the model that the stored procedure will return two collections, one of blog entries and one of post entries.</span></span>

-   <span data-ttu-id="627de-145">関数マッピング要素を検索します。</span><span class="sxs-lookup"><span data-stu-id="627de-145">Find the function mapping element:</span></span>

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

-   <span data-ttu-id="627de-146">結果のマッピングを、返される各エンティティの1つに置き換えます。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="627de-146">Replace the result mapping with one for each entity being returned, such as the following:</span></span>

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

<span data-ttu-id="627de-147">また、既定で作成されたものなど、結果セットを複合型にマップすることもできます。</span><span class="sxs-lookup"><span data-stu-id="627de-147">It is also possible to map the result sets to complex types, such as the one created by default.</span></span> <span data-ttu-id="627de-148">これを行うには、新しい複合型を削除する代わりに作成し、上記の例のエンティティ名を使用していたあらゆる場所で複合型を使用します。</span><span class="sxs-lookup"><span data-stu-id="627de-148">To do this you create a new complex type, instead of removing them, and use the complex types everywhere that you had used the entity names in the examples above.</span></span>

<span data-ttu-id="627de-149">これらのマッピングが変更されたら、モデルを保存し、次のコードを実行してストアドプロシージャを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="627de-149">Once these mappings have been changed then you can save the model and execute the following code to use the stored procedure:</span></span>

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
> <span data-ttu-id="627de-150">モデルの edmx ファイルを手動で編集した場合は、データベースからモデルを再生成すると上書きされます。</span><span class="sxs-lookup"><span data-stu-id="627de-150">If you manually edit the edmx file for your model it will be overwritten if you ever regenerate the model from the database.</span></span>

## <a name="summary"></a><span data-ttu-id="627de-151">まとめ</span><span class="sxs-lookup"><span data-stu-id="627de-151">Summary</span></span>

<span data-ttu-id="627de-152">ここでは、Entity Framework を使用して複数の結果セットにアクセスする2つの異なる方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="627de-152">Here we have shown two different methods of accessing multiple result sets using Entity Framework.</span></span> <span data-ttu-id="627de-153">どちらも、状況や好みに応じて同じように有効であり、状況に適したものを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="627de-153">Both of them are equally valid depending on your situation and preferences and you should choose the one that seems best for your circumstances.</span></span> <span data-ttu-id="627de-154">Entity Framework の将来のバージョンでは、複数の結果セットのサポートが改善される予定です。このドキュメントの手順を実行する必要はなくなります。</span><span class="sxs-lookup"><span data-stu-id="627de-154">It is planned that the support for multiple result sets will be improved in future versions of Entity Framework and that performing the steps in this document will no longer be necessary.</span></span>
