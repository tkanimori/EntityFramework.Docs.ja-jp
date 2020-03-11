---
title: 複数の結果セットを持つストアドプロシージャ-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 1b3797f9-cd3d-4752-a55e-47b84b399dc1
ms.openlocfilehash: 098ed88ba52e211965baf3660f0e51bd74c71efd
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415455"
---
# <a name="stored-procedures-with-multiple-result-sets"></a><span data-ttu-id="25a00-102">複数の結果セットを持つストアドプロシージャ</span><span class="sxs-lookup"><span data-stu-id="25a00-102">Stored Procedures with Multiple Result Sets</span></span>
<span data-ttu-id="25a00-103">ストアドプロシージャを使用する場合、複数の結果セットを返す必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="25a00-103">Sometimes when using stored procedures you will need to return more than one result set.</span></span> <span data-ttu-id="25a00-104">このシナリオは、1つの画面を作成するために必要なデータベースラウンドトリップの数を減らすためによく使用されます。</span><span class="sxs-lookup"><span data-stu-id="25a00-104">This scenario is commonly used to reduce the number of database round trips required to compose a single screen.</span></span><span data-ttu-id="25a00-105"> EF5 より前の Entity Framework では、ストアドプロシージャを呼び出すことができますが、呼び出し元のコードに返されるのは最初の結果セットだけです。</span><span class="sxs-lookup"><span data-stu-id="25a00-105"> Prior to EF5, Entity Framework would allow the stored procedure to be called but would only return the first result set to the calling code.</span></span>

<span data-ttu-id="25a00-106">この記事では、を使用して Entity Framework のストアドプロシージャから複数の結果セットにアクセスする2つの方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="25a00-106">This article will show you two ways that you can use to access more than one result set from a stored procedure in Entity Framework.</span></span> <span data-ttu-id="25a00-107">1つはコードだけを使用し、Code first と EF デザイナーの両方で動作し、EF デザイナーでのみ動作するものです。</span><span class="sxs-lookup"><span data-stu-id="25a00-107">One that uses just code and works with both Code first and the EF Designer and one that only works with the EF Designer.</span></span> <span data-ttu-id="25a00-108">このためのツールと API のサポートは、今後のバージョンの Entity Framework で改善されます。</span><span class="sxs-lookup"><span data-stu-id="25a00-108">The tooling and API support for this should improve in future versions of Entity Framework.</span></span>

## <a name="model"></a><span data-ttu-id="25a00-109">モデル</span><span class="sxs-lookup"><span data-stu-id="25a00-109">Model</span></span>

<span data-ttu-id="25a00-110">この記事の例では、ブログに多数の投稿があり、投稿が1つのブログに属している、基本的なブログと投稿モデルを使用しています。</span><span class="sxs-lookup"><span data-stu-id="25a00-110">The examples in this article use a basic Blog and Posts model where a blog has many posts and a post belongs to a single blog.</span></span> <span data-ttu-id="25a00-111">次のように、すべてのブログと投稿を返すストアドプロシージャをデータベースに使用します。</span><span class="sxs-lookup"><span data-stu-id="25a00-111">We will use a stored procedure in the database that returns all blogs and posts, something like this:</span></span>

``` SQL
    CREATE PROCEDURE [dbo].[GetAllBlogsAndPosts]
    AS
        SELECT * FROM dbo.Blogs
        SELECT * FROM dbo.Posts
```

## <a name="accessing-multiple-result-sets-with-code"></a><span data-ttu-id="25a00-112">コードを使用した複数の結果セットへのアクセス</span><span class="sxs-lookup"><span data-stu-id="25a00-112">Accessing Multiple Result Sets with Code</span></span>

<span data-ttu-id="25a00-113">コードを実行して、ストアドプロシージャを実行するための未加工の SQL コマンドを発行できます。</span><span class="sxs-lookup"><span data-stu-id="25a00-113">We can execute use code to issue a raw SQL command to execute our stored procedure.</span></span> <span data-ttu-id="25a00-114">この方法の利点は、Code first と EF Designer の両方で動作することです。</span><span class="sxs-lookup"><span data-stu-id="25a00-114">The advantage of this approach is that it works with both Code first and the EF Designer.</span></span>

<span data-ttu-id="25a00-115">複数の結果セットを機能させるには、IObjectContextAdapter インターフェイスを使用して、ObjectContext API にドロップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="25a00-115">In order to get multiple result sets working we need to drop to the ObjectContext API by using the IObjectContextAdapter interface.</span></span>

<span data-ttu-id="25a00-116">ObjectContext が得られたら、Translate メソッドを使用して、ストアドプロシージャの結果を、EF で通常どおりに追跡および使用できるエンティティに変換できます。</span><span class="sxs-lookup"><span data-stu-id="25a00-116">Once we have an ObjectContext then we can use the Translate method to translate the results of our stored procedure into entities that can be tracked and used in EF as normal.</span></span> <span data-ttu-id="25a00-117">次のコードサンプルは、実際の動作を示しています。</span><span class="sxs-lookup"><span data-stu-id="25a00-117">The following code sample demonstrates this in action.</span></span>

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

<span data-ttu-id="25a00-118">Translate メソッドは、プロシージャを実行したときに受け取ったリーダー、EntitySet 名、および MergeOption を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="25a00-118">The Translate method accepts the reader that we received when we executed the procedure, an EntitySet name, and a MergeOption.</span></span> <span data-ttu-id="25a00-119">EntitySet 名は、派生コンテキストの DbSet プロパティと同じになります。</span><span class="sxs-lookup"><span data-stu-id="25a00-119">The EntitySet name will be the same as the DbSet property on your derived context.</span></span> <span data-ttu-id="25a00-120">MergeOption 列挙型は、同じエンティティが既にメモリに存在する場合に結果を処理する方法を制御します。</span><span class="sxs-lookup"><span data-stu-id="25a00-120">The MergeOption enum controls how results are handled if the same entity already exists in memory.</span></span>

<span data-ttu-id="25a00-121">ここでは、次の結果セットに移動する前に最初の結果セットを使用する必要があるため、NextResult を呼び出す前にブログのコレクションを反復処理しています。</span><span class="sxs-lookup"><span data-stu-id="25a00-121">Here we iterate through the collection of blogs before we call NextResult, this is important given the above code because the first result set must be consumed before moving to the next result set.</span></span>

<span data-ttu-id="25a00-122">2つの translate メソッドが呼び出されると、他のエンティティと同じ方法でブログおよび投稿エンティティが、EF によって追跡されます。そのため、変更または削除したり、通常どおりに保存したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="25a00-122">Once the two translate methods are called then the Blog and Post entities are tracked by EF the same way as any other entity and so can be modified or deleted and saved as normal.</span></span>

>[!NOTE]
> <span data-ttu-id="25a00-123">EF では、Translate メソッドを使用してエンティティを作成するときに、マッピングは考慮されません。</span><span class="sxs-lookup"><span data-stu-id="25a00-123">EF does not take any mapping into account when it creates entities using the Translate method.</span></span> <span data-ttu-id="25a00-124">結果セットの列名と、クラスのプロパティ名が一致するだけです。</span><span class="sxs-lookup"><span data-stu-id="25a00-124">It will simply match column names in the result set with property names on your classes.</span></span>

>[!NOTE]
> <span data-ttu-id="25a00-125">遅延読み込みが有効になっている場合、いずれかのブログエンティティの [投稿] プロパティにアクセスすると、EF はデータベースに接続し、すべての投稿を既に読み込んでいる場合でも、すべての投稿を遅延読み込みします。</span><span class="sxs-lookup"><span data-stu-id="25a00-125">That if you have lazy loading enabled, accessing the posts property on one of the blog entities then EF will connect to the database to lazily load all posts, even though we have already loaded them all.</span></span> <span data-ttu-id="25a00-126">これは、すべての投稿が読み込まれたかどうか、またはデータベースにさらにがあるかどうかを EF が認識できないためです。</span><span class="sxs-lookup"><span data-stu-id="25a00-126">This is because EF cannot know whether or not you have loaded all posts or if there are more in the database.</span></span> <span data-ttu-id="25a00-127">これを回避するには、遅延読み込みを無効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="25a00-127">If you want to avoid this then you will need to disable lazy loading.</span></span>

## <a name="multiple-result-sets-with-configured-in-edmx"></a><span data-ttu-id="25a00-128">EDMX で構成された複数の結果セット</span><span class="sxs-lookup"><span data-stu-id="25a00-128">Multiple Result Sets with Configured in EDMX</span></span>

>[!NOTE]
> <span data-ttu-id="25a00-129">EDMX で複数の結果セットを構成できるようにするには、.NET Framework 4.5 をターゲットにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="25a00-129">You must target .NET Framework 4.5 to be able to configure multiple result sets in EDMX.</span></span> <span data-ttu-id="25a00-130">.NET 4.0 を対象としている場合は、前のセクションで示したコードベースのメソッドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="25a00-130">If you are targeting .NET 4.0 you can use the code-based method shown in the previous section.</span></span>

<span data-ttu-id="25a00-131">EF デザイナーを使用している場合は、返されるさまざまな結果セットが認識されるようにモデルを変更することもできます。</span><span class="sxs-lookup"><span data-stu-id="25a00-131">If you are using the EF Designer, you can also modify your model so that it knows about the different result sets that will be returned.</span></span> <span data-ttu-id="25a00-132">前に知っておくべきことは、ツールが複数の結果セットに対応しているわけではないことです。そのため、edmx ファイルを手動で編集する必要があります。</span><span class="sxs-lookup"><span data-stu-id="25a00-132">One thing to know before hand is that the tooling is not multiple result set aware, so you will need to manually edit the edmx file.</span></span> <span data-ttu-id="25a00-133">このような edmx ファイルの編集は機能しますが、VS でモデルの検証も中断されます。</span><span class="sxs-lookup"><span data-stu-id="25a00-133">Editing the edmx file like this will work but it will also break the validation of the model in VS.</span></span> <span data-ttu-id="25a00-134">そのため、モデルを検証すると、常にエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="25a00-134">So if you validate your model you will always get errors.</span></span>

-   <span data-ttu-id="25a00-135">このためには、単一の結果セットクエリの場合と同じように、ストアドプロシージャをモデルに追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="25a00-135">In order to do this you need to add the stored procedure to your model as you would for a single result set query.</span></span>
-   <span data-ttu-id="25a00-136">これが完了したら、モデルを右クリックし、[**ファイルを開くアプリケーションの**選択] を選択します。</span><span class="sxs-lookup"><span data-stu-id="25a00-136">Once you have this then you need to right click on your model and select **Open With..**</span></span> <span data-ttu-id="25a00-137">次に**Xml**</span><span class="sxs-lookup"><span data-stu-id="25a00-137">then **Xml**</span></span>

    ![名前を付けて開く](~/ef6/media/openas.png)

<span data-ttu-id="25a00-139">モデルを XML として開いたら、次の手順を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="25a00-139">Once you have the model opened as XML then you need to do the following steps:</span></span>

-   <span data-ttu-id="25a00-140">モデル内の複合型と関数インポートを検索します。</span><span class="sxs-lookup"><span data-stu-id="25a00-140">Find the complex type and function import in your model:</span></span>

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

 

-   <span data-ttu-id="25a00-141">複合型を削除します。</span><span class="sxs-lookup"><span data-stu-id="25a00-141">Remove the complex type</span></span>
-   <span data-ttu-id="25a00-142">関数インポートを更新して、エンティティにマップされるようにします。この場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="25a00-142">Update the function import so that it maps to your entities, in our case it will look like the following:</span></span>

``` xml
    <FunctionImport Name="GetAllBlogsAndPosts">
      <ReturnType EntitySet="Blogs" Type="Collection(BlogModel.Blog)" />
      <ReturnType EntitySet="Posts" Type="Collection(BlogModel.Post)" />
    </FunctionImport>
```

<span data-ttu-id="25a00-143">これにより、ストアドプロシージャが2つのコレクション (ブログエントリの1つと投稿エントリの1つ) を返すことがモデルに通知されます。</span><span class="sxs-lookup"><span data-stu-id="25a00-143">This tells the model that the stored procedure will return two collections, one of blog entries and one of post entries.</span></span>

-   <span data-ttu-id="25a00-144">関数マッピング要素を検索します。</span><span class="sxs-lookup"><span data-stu-id="25a00-144">Find the function mapping element:</span></span>

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

-   <span data-ttu-id="25a00-145">結果のマッピングを、返される各エンティティの1つに置き換えます。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="25a00-145">Replace the result mapping with one for each entity being returned, such as the following:</span></span>

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

<span data-ttu-id="25a00-146">また、既定で作成されたものなど、結果セットを複合型にマップすることもできます。</span><span class="sxs-lookup"><span data-stu-id="25a00-146">It is also possible to map the result sets to complex types, such as the one created by default.</span></span> <span data-ttu-id="25a00-147">これを行うには、新しい複合型を削除する代わりに作成し、上記の例のエンティティ名を使用していたあらゆる場所で複合型を使用します。</span><span class="sxs-lookup"><span data-stu-id="25a00-147">To do this you create a new complex type, instead of removing them, and use the complex types everywhere that you had used the entity names in the examples above.</span></span>

<span data-ttu-id="25a00-148">これらのマッピングが変更されたら、モデルを保存し、次のコードを実行してストアドプロシージャを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="25a00-148">Once these mappings have been changed then you can save the model and execute the following code to use the stored procedure:</span></span>

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
> <span data-ttu-id="25a00-149">モデルの edmx ファイルを手動で編集した場合は、データベースからモデルを再生成すると上書きされます。</span><span class="sxs-lookup"><span data-stu-id="25a00-149">If you manually edit the edmx file for your model it will be overwritten if you ever regenerate the model from the database.</span></span>

## <a name="summary"></a><span data-ttu-id="25a00-150">まとめ</span><span class="sxs-lookup"><span data-stu-id="25a00-150">Summary</span></span>

<span data-ttu-id="25a00-151">ここでは、Entity Framework を使用して複数の結果セットにアクセスする2つの異なる方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="25a00-151">Here we have shown two different methods of accessing multiple result sets using Entity Framework.</span></span> <span data-ttu-id="25a00-152">どちらも、状況や好みに応じて同じように有効であり、状況に適したものを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="25a00-152">Both of them are equally valid depending on your situation and preferences and you should choose the one that seems best for your circumstances.</span></span> <span data-ttu-id="25a00-153">Entity Framework の将来のバージョンでは、複数の結果セットのサポートが改善される予定です。このドキュメントの手順を実行する必要はなくなります。</span><span class="sxs-lookup"><span data-stu-id="25a00-153">It is planned that the support for multiple result sets will be improved in future versions of Entity Framework and that performing the steps in this document will no longer be necessary.</span></span>
