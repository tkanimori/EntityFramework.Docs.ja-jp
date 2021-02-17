---
title: 事前に生成されたマッピングビュー-EF6
description: Entity Framework 6 で事前に生成されたマッピングビュー
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/performance/pre-generated-views
ms.openlocfilehash: bea0cdc59161068a8186ad2106516ba4f34910a9
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543147"
---
# <a name="pre-generated-mapping-views"></a><span data-ttu-id="18e68-103">事前に生成されたマッピングビュー</span><span class="sxs-lookup"><span data-stu-id="18e68-103">Pre-generated mapping views</span></span>
<span data-ttu-id="18e68-104">Entity Framework がクエリを実行したり、データソースへの変更を保存したりする前に、データベースにアクセスするための一連のマッピングビューを生成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="18e68-104">Before the Entity Framework can execute a query or save changes to the data source, it must generate a set of mapping views to access the database.</span></span> <span data-ttu-id="18e68-105">これらのマッピングビューは、データベースを抽象的な方法で表す一連の Entity SQL ステートメントであり、アプリケーションドメインごとにキャッシュされるメタデータの一部です。</span><span class="sxs-lookup"><span data-stu-id="18e68-105">These mapping views are a set of Entity SQL statement that represent the database in an abstract way, and are part of the metadata which is cached per application domain.</span></span> <span data-ttu-id="18e68-106">同じアプリケーションドメイン内に同じコンテキストの複数のインスタンスを作成すると、キャッシュされたメタデータからのマッピングビューが再生成されるのではなく再利用されます。</span><span class="sxs-lookup"><span data-stu-id="18e68-106">If you create multiple instances of the same context in the same application domain, they will reuse mapping views from the cached metadata rather than regenerating them.</span></span> <span data-ttu-id="18e68-107">マッピングビューの生成は最初のクエリの実行にかかる全体的なコストの重要な部分であるため、Entity Framework を使用すると、マッピングビューを事前に生成し、コンパイル済みのプロジェクトに含めることができます。</span><span class="sxs-lookup"><span data-stu-id="18e68-107">Because mapping view generation is a significant part of the overall cost of executing the first query, the Entity Framework enables you to pre-generate mapping views and include them in the compiled project.</span></span> <span data-ttu-id="18e68-108">詳細については、「  [パフォーマンスに関する考慮事項 (Entity Framework)](xref:ef6/fundamentals/performance/perf-whitepaper)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18e68-108">For more information, see  [Performance Considerations (Entity Framework)](xref:ef6/fundamentals/performance/perf-whitepaper).</span></span>

## <a name="generating-mapping-views-with-the-ef-power-tools-community-edition"></a><span data-ttu-id="18e68-109">EF Power Tools Community Edition を使用したマッピングビューの生成</span><span class="sxs-lookup"><span data-stu-id="18e68-109">Generating Mapping Views with the EF Power Tools Community Edition</span></span>

<span data-ttu-id="18e68-110">ビューを事前に生成する最も簡単な方法は、 [EF Power Tools Community エディション](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition)を使用することです。</span><span class="sxs-lookup"><span data-stu-id="18e68-110">The easiest way to pre-generate views is to use the [EF Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition).</span></span> <span data-ttu-id="18e68-111">パワーツールをインストールすると、次のように、ビューを生成するメニューオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="18e68-111">Once you have the Power Tools installed you will have a menu option to Generate Views, as below.</span></span>

-   <span data-ttu-id="18e68-112">**Code First** モデルの場合は、dbcontext クラスを含むコードファイルを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="18e68-112">For **Code First** models right-click on the code file that contains your DbContext class.</span></span>
-   <span data-ttu-id="18e68-113">**EF デザイナー** モデルの場合は、EDMX ファイルを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="18e68-113">For **EF Designer** models right-click on your EDMX file.</span></span>

![ビューの生成](~/ef6/media/generateviews.png)

<span data-ttu-id="18e68-115">プロセスが完了すると、次のようなクラスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="18e68-115">Once the process is finished you will have a class similar to the following generated</span></span>

![生成されたビュー](~/ef6/media/generatedviews.png)

<span data-ttu-id="18e68-117">アプリケーションを実行すると、EF はこのクラスを使用して必要に応じてビューを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="18e68-117">Now when you run your application EF will use this class to load views as required.</span></span> <span data-ttu-id="18e68-118">モデルが変更され、このクラスを再生成していない場合、EF は例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="18e68-118">If your model changes and you do not re-generate this class then EF will throw an exception.</span></span>

## <a name="generating-mapping-views-from-code---ef6-onwards"></a><span data-ttu-id="18e68-119">コード EF6 からのマッピングビューの生成</span><span class="sxs-lookup"><span data-stu-id="18e68-119">Generating Mapping Views from Code - EF6 Onwards</span></span>

<span data-ttu-id="18e68-120">ビューを生成するもう1つの方法は、EF に用意されている Api を使用することです。</span><span class="sxs-lookup"><span data-stu-id="18e68-120">The other way to generate views is to use the APIs that EF provides.</span></span> <span data-ttu-id="18e68-121">この方法を使用する場合は、必要に応じてビューを自由にシリアル化できますが、自分でビューを読み込む必要もあります。</span><span class="sxs-lookup"><span data-stu-id="18e68-121">When using this method you have the freedom to serialize the views however you like, but you also need to load the views yourself.</span></span>

> [!NOTE]
> <span data-ttu-id="18e68-122">**EF6 以降のみ** -このセクションに示されている api は Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="18e68-122">**EF6 Onwards Only** - The APIs shown in this section were introduced in Entity Framework 6.</span></span> <span data-ttu-id="18e68-123">以前のバージョンを使用している場合、この情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="18e68-123">If you are using an earlier version this information does not apply.</span></span>

### <a name="generating-views"></a><span data-ttu-id="18e68-124">ビューの生成</span><span class="sxs-lookup"><span data-stu-id="18e68-124">Generating Views</span></span>

<span data-ttu-id="18e68-125">ビューを生成するための Api は、StorageMappingItemCollection クラスに表示されます。</span><span class="sxs-lookup"><span data-stu-id="18e68-125">The APIs to generate views are on the System.Data.Entity.Core.Mapping.StorageMappingItemCollection class.</span></span> <span data-ttu-id="18e68-126">ObjectContext の MetadataWorkspace を使用して、コンテキストの StorageMappingCollection を取得できます。</span><span class="sxs-lookup"><span data-stu-id="18e68-126">You can retrieve a StorageMappingCollection for a Context by using the MetadataWorkspace of an ObjectContext.</span></span> <span data-ttu-id="18e68-127">新しい DbContext API を使用している場合は、次のような IObjectContextAdapter を使用してこれにアクセスできます。このコードでは、dbContext という名前の派生 DbContext のインスタンスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="18e68-127">If you are using the newer DbContext API then you can access this by using the IObjectContextAdapter like below, in this code we have an instance of your derived DbContext called dbContext:</span></span>

``` csharp
    var objectContext = ((IObjectContextAdapter) dbContext).ObjectContext;
    var  mappingCollection = (StorageMappingItemCollection)objectContext.MetadataWorkspace
                                                                        .GetItemCollection(DataSpace.CSSpace);
```

<span data-ttu-id="18e68-128">StorageMappingItemCollection を取得すると、GenerateViews と ComputeMappingHashValue メソッドにアクセスできるようになります。</span><span class="sxs-lookup"><span data-stu-id="18e68-128">Once you have the StorageMappingItemCollection then you can get access to the GenerateViews and ComputeMappingHashValue methods.</span></span>

``` csharp
    public Dictionary<EntitySetBase, DbMappingView> GenerateViews(IList<EdmSchemaError> errors)
    public string ComputeMappingHashValue()
```

<span data-ttu-id="18e68-129">最初のメソッドは、コンテナーマッピング内の各ビューのエントリを含むディクショナリを作成します。</span><span class="sxs-lookup"><span data-stu-id="18e68-129">The first method creates a dictionary with an entry for each view in the container mapping.</span></span> <span data-ttu-id="18e68-130">2番目のメソッドは、1つのコンテナーマッピングのハッシュ値を計算し、ビューが事前に生成された後にモデルが変更されていないことを検証するために実行時に使用されます。</span><span class="sxs-lookup"><span data-stu-id="18e68-130">The second method computes a hash value for the single container mapping and is used at runtime to validate that the model has not changed since the views were pre-generated.</span></span> <span data-ttu-id="18e68-131">複数のコンテナーマッピングが関係する複雑なシナリオでは、2つのメソッドのオーバーライドが提供されます。</span><span class="sxs-lookup"><span data-stu-id="18e68-131">Overrides of the two methods are provided for complex scenarios involving multiple container mappings.</span></span>

<span data-ttu-id="18e68-132">ビューを生成するときに、GenerateViews メソッドを呼び出し、結果として得られた EntitySetBase および DbMappingView を記述します。</span><span class="sxs-lookup"><span data-stu-id="18e68-132">When generating views you will call the GenerateViews method and then write out the resulting EntitySetBase and DbMappingView.</span></span> <span data-ttu-id="18e68-133">また、ComputeMappingHashValue メソッドによって生成されたハッシュも格納する必要があります。</span><span class="sxs-lookup"><span data-stu-id="18e68-133">You will also need to store the hash generated by the ComputeMappingHashValue method.</span></span>

### <a name="loading-views"></a><span data-ttu-id="18e68-134">ビューの読み込み</span><span class="sxs-lookup"><span data-stu-id="18e68-134">Loading Views</span></span>

<span data-ttu-id="18e68-135">GenerateViews メソッドによって生成されたビューを読み込むには、DbMappingViewCache 抽象クラスを継承するクラスを EF に提供します。</span><span class="sxs-lookup"><span data-stu-id="18e68-135">In order to load the views generated by the GenerateViews method, you can provide EF with a class that inherits from the DbMappingViewCache abstract class.</span></span> <span data-ttu-id="18e68-136">DbMappingViewCache は、実装する必要がある2つのメソッドを指定します。</span><span class="sxs-lookup"><span data-stu-id="18e68-136">DbMappingViewCache specifies two methods that you must implement:</span></span>

``` csharp
    public abstract string MappingHashValue { get; }
    public abstract DbMappingView GetView(EntitySetBase extent);
```

<span data-ttu-id="18e68-137">MappingHashValue プロパティは、ComputeMappingHashValue メソッドによって生成されたハッシュを返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="18e68-137">The MappingHashValue property must return the hash generated by the ComputeMappingHashValue method.</span></span> <span data-ttu-id="18e68-138">EF がビューを要求するときに、最初に生成され、このプロパティによって返されるハッシュとモデルのハッシュ値を比較します。</span><span class="sxs-lookup"><span data-stu-id="18e68-138">When EF is going to ask for views it will first generate and compare the hash value of the model with the hash returned by this property.</span></span> <span data-ttu-id="18e68-139">一致しない場合、EF は EntityCommandCompilationException 例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="18e68-139">If they do not match then EF will throw an EntityCommandCompilationException exception.</span></span>

<span data-ttu-id="18e68-140">GetView メソッドは EntitySetBase を受け入れ、GenerateViews メソッドによって生成されたディクショナリ内の指定された EntitySetBase に関連付けられていた EntitySql を含む DbMappingVIew を返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="18e68-140">The GetView method will accept an EntitySetBase and you need to return a DbMappingVIew containing the EntitySql that was generated for that was associated with the given EntitySetBase in the dictionary generated by the GenerateViews method.</span></span> <span data-ttu-id="18e68-141">EF が不要なビューを要求した場合、GetView は null を返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="18e68-141">If EF asks for a view that you do not have then GetView should return null.</span></span>

<span data-ttu-id="18e68-142">次に示すのは、前述のようにパワーツールで生成された DbMappingViewCache からの抽出です。ここでは、必要な EntitySql を格納および取得するための1つの方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="18e68-142">The following is an extract from the DbMappingViewCache that is generated with the Power Tools as described above, in it we see one way to store and retrieve the EntitySql required.</span></span>

``` csharp
    public override string MappingHashValue
    {
        get { return "a0b843f03dd29abee99789e190a6fb70ce8e93dc97945d437d9a58fb8e2afd2e"; }
    }

    public override DbMappingView GetView(EntitySetBase extent)
    {
        if (extent == null)
        {
            throw new ArgumentNullException("extent");
        }

        var extentName = extent.EntityContainer.Name + "." + extent.Name;

        if (extentName == "BlogContext.Blogs")
        {
            return GetView2();
        }

        if (extentName == "BlogContext.Posts")
        {
            return GetView3();
        }

        return null;
    }

    private static DbMappingView GetView2()
    {
        return new DbMappingView(@"
            SELECT VALUE -- Constructing Blogs
            [BlogApp.Models.Blog](T1.Blog_BlogId, T1.Blog_Test, T1.Blog_title, T1.Blog_Active, T1.Blog_SomeDecimal)
            FROM (
            SELECT
                T.BlogId AS Blog_BlogId,
                T.Test AS Blog_Test,
                T.title AS Blog_title,
                T.Active AS Blog_Active,
                T.SomeDecimal AS Blog_SomeDecimal,
                True AS _from0
            FROM CodeFirstDatabase.Blog AS T
            ) AS T1");
    }
```

<span data-ttu-id="18e68-143">EF で DbMappingViewCache を使用するには、DbMappingViewCacheTypeAttribute を使用して、作成されたコンテキストを指定します。</span><span class="sxs-lookup"><span data-stu-id="18e68-143">To have EF use your DbMappingViewCache you add use the DbMappingViewCacheTypeAttribute, specifying the context that it was created for.</span></span> <span data-ttu-id="18e68-144">次のコードでは、ブログコンテキストを MyMappingViewCache クラスに関連付けます。</span><span class="sxs-lookup"><span data-stu-id="18e68-144">In the code below we associate the BlogContext with the MyMappingViewCache class.</span></span>

``` csharp
    [assembly: DbMappingViewCacheType(typeof(BlogContext), typeof(MyMappingViewCache))]
```

<span data-ttu-id="18e68-145">より複雑なシナリオでは、マッピングビューのキャッシュファクトリを指定することによって、マッピングビューのキャッシュインスタンスを指定できます。</span><span class="sxs-lookup"><span data-stu-id="18e68-145">For more complex scenarios, mapping view cache instances can be provided by specifying a mapping view cache factory.</span></span> <span data-ttu-id="18e68-146">これは、抽象クラスの system.object を実装することによって行うことができます。。</span><span class="sxs-lookup"><span data-stu-id="18e68-146">This can be done by implementing the abstract class System.Data.Entity.Infrastructure.MappingViews.DbMappingViewCacheFactory.</span></span> <span data-ttu-id="18e68-147">使用されるマッピングビューのキャッシュファクトリのインスタンスは、StorageMappingItemCollection. MappingViewCacheFactoryproperty を使用して取得または設定できます。</span><span class="sxs-lookup"><span data-stu-id="18e68-147">The instance of the mapping view cache factory that is used can be retrieved or set using the StorageMappingItemCollection.MappingViewCacheFactoryproperty.</span></span>
