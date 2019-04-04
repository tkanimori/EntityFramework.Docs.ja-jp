---
title: 事前に生成されたマッピング ビュー - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 917ba9c8-6ddf-4631-ab8c-c4fb378c2fcd
ms.openlocfilehash: 1fda9fe9638adce9b24a6b81aa081effeb0def81
ms.sourcegitcommit: c568d33214fc25c76e02c8529a29da7a356b37b4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2018
ms.locfileid: "47459527"
---
# <a name="pre-generated-mapping-views"></a><span data-ttu-id="da5fe-102">事前に生成されたマッピング ビュー</span><span class="sxs-lookup"><span data-stu-id="da5fe-102">Pre-generated mapping views</span></span>
<span data-ttu-id="da5fe-103">Entity Framework では、クエリを実行したり、データ ソースに変更を保存することが、前に、一連のデータベースにアクセスするマッピング ビューが生成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="da5fe-103">Before the Entity Framework can execute a query or save changes to the data source, it must generate a set of mapping views to access the database.</span></span> <span data-ttu-id="da5fe-104">これらのマッピング ビューを抽象的な方法でデータベースを表す Entity SQL ステートメントのセットし、アプリケーション ドメインごとにキャッシュされるメタデータの一部であります。</span><span class="sxs-lookup"><span data-stu-id="da5fe-104">These mapping views are a set of Entity SQL statement that represent the database in an abstract way, and are part of the metadata which is cached per application domain.</span></span> <span data-ttu-id="da5fe-105">同じアプリケーション ドメインで同じコンテキストの複数のインスタンスを作成する場合は、再生成するのではなく、キャッシュされたメタデータからマッピング ビューは再利用します。</span><span class="sxs-lookup"><span data-stu-id="da5fe-105">If you create multiple instances of the same context in the same application domain, they will reuse mapping views from the cached metadata rather than regenerating them.</span></span> <span data-ttu-id="da5fe-106">マップ ビューの生成は、最初のクエリの実行における全体的なコストのかなりの部分であるため、Entity Framework では、マッピング ビューを事前に生成およびコンパイル済みのプロジェクトに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="da5fe-106">Because mapping view generation is a significant part of the overall cost of executing the first query, the Entity Framework enables you to pre-generate mapping views and include them in the compiled project.</span></span> <span data-ttu-id="da5fe-107">詳細については、[パフォーマンスに関する考慮事項 (Entity Framework)](~/ef6/fundamentals/performance/perf-whitepaper.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="da5fe-107">For more information, see  [Performance Considerations (Entity Framework)](~/ef6/fundamentals/performance/perf-whitepaper.md).</span></span>

## <a name="generating-mapping-views-with-the-ef-power-tools-community-edition"></a><span data-ttu-id="da5fe-108">EF Power Tools の Community Edition ではビューのマッピングを生成します。</span><span class="sxs-lookup"><span data-stu-id="da5fe-108">Generating Mapping Views with the EF Power Tools Community Edition</span></span>

<span data-ttu-id="da5fe-109">ビューを事前に生成する最も簡単な方法は使用する、 [EF Power Tools の Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition)します。</span><span class="sxs-lookup"><span data-stu-id="da5fe-109">The easiest way to pre-generate views is to use the [EF Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition).</span></span> <span data-ttu-id="da5fe-110">Power ツールをインストールしたら次に示すようにビューの生成のメニュー オプションを必要があります。</span><span class="sxs-lookup"><span data-stu-id="da5fe-110">Once you have the Power Tools installed you will have a menu option to Generate Views, as below.</span></span>

-   <span data-ttu-id="da5fe-111">**Code First** DbContext クラスを含むコード ファイルにモデルを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="da5fe-111">For **Code First** models right-click on the code file that contains your DbContext class.</span></span>
-   <span data-ttu-id="da5fe-112">**EF Designer**モデルは、EDMX ファイルを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="da5fe-112">For **EF Designer** models right-click on your EDMX file.</span></span>

![ビューを生成します。](~/ef6/media/generateviews.png)

<span data-ttu-id="da5fe-114">生成された次のようなクラスが、プロセスが完了すると</span><span class="sxs-lookup"><span data-stu-id="da5fe-114">Once the process is finished you will have a class similar to the following generated</span></span>

![生成されたビュー](~/ef6/media/generatedviews.png)

<span data-ttu-id="da5fe-116">これを実行すると、アプリケーションの EF は必要に応じてビューの読み込みにこのクラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="da5fe-116">Now when you run your application EF will use this class to load views as required.</span></span> <span data-ttu-id="da5fe-117">モデルの変更と再生成しないこのクラスの場合、EF によって例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="da5fe-117">If your model changes and you do not re-generate this class then EF will throw an exception.</span></span>

## <a name="generating-mapping-views-from-code---ef6-onwards"></a><span data-ttu-id="da5fe-118">コードの EF6 以降からビューのマッピングを生成します。</span><span class="sxs-lookup"><span data-stu-id="da5fe-118">Generating Mapping Views from Code - EF6 Onwards</span></span>

<span data-ttu-id="da5fe-119">ビューを生成するその他の方法は、EF を提供する Api を使用することです。</span><span class="sxs-lookup"><span data-stu-id="da5fe-119">The other way to generate views is to use the APIs that EF provides.</span></span> <span data-ttu-id="da5fe-120">このメソッドを使用する場合が、自分でビューの読み込みにも必要ですが、ビューをシリアル化する自由があります。</span><span class="sxs-lookup"><span data-stu-id="da5fe-120">When using this method you have the freedom to serialize the views however you like, but you also need to load the views yourself.</span></span>

> [!NOTE]
> <span data-ttu-id="da5fe-121">**EF6 以降のみ**-このセクションに示すように、Api は、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="da5fe-121">**EF6 Onwards Only** - The APIs shown in this section were introduced in Entity Framework 6.</span></span> <span data-ttu-id="da5fe-122">以前のバージョンを使用している場合、この情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="da5fe-122">If you are using an earlier version this information does not apply.</span></span>

### <a name="generating-views"></a><span data-ttu-id="da5fe-123">ビューの生成</span><span class="sxs-lookup"><span data-stu-id="da5fe-123">Generating Views</span></span>

<span data-ttu-id="da5fe-124">ビューを生成する Api は、System.Data.Entity.Core.Mapping.StorageMappingItemCollection クラスです。</span><span class="sxs-lookup"><span data-stu-id="da5fe-124">The APIs to generate views are on the System.Data.Entity.Core.Mapping.StorageMappingItemCollection class.</span></span> <span data-ttu-id="da5fe-125">ObjectContext の MetadataWorkspace を使用して、コンテキストに対して、StorageMappingCollection を取得できます。</span><span class="sxs-lookup"><span data-stu-id="da5fe-125">You can retrieve a StorageMappingCollection for a Context by using the MetadataWorkspace of an ObjectContext.</span></span> <span data-ttu-id="da5fe-126">使用してこれにアクセスできるように、新しい DbContext API を使用している場合は、次のような IObjectContextAdapter、このコードであるという dbContext 派生、DbContext のインスタンス。</span><span class="sxs-lookup"><span data-stu-id="da5fe-126">If you are using the newer DbContext API then you can access this by using the IObjectContextAdapter like below, in this code we have an instance of your derived DbContext called dbContext:</span></span>

``` csharp
    var objectContext = ((IObjectContextAdapter) dbContext).ObjectContext;
    var  mappingCollection = (StorageMappingItemCollection)objectContext.MetadataWorkspace
                                                                        .GetItemCollection(DataSpace.CSSpace);
```

<span data-ttu-id="da5fe-127">StorageMappingItemCollection したら GenerateViews と ComputeMappingHashValue メソッドへのアクセスを取得できます。</span><span class="sxs-lookup"><span data-stu-id="da5fe-127">Once you have the StorageMappingItemCollection then you can get access to the GenerateViews and ComputeMappingHashValue methods.</span></span>

``` csharp
    public Dictionary\<EntitySetBase, DbMappingView> GenerateViews(IList<EdmSchemaError> errors)
    public string ComputeMappingHashValue()
```

<span data-ttu-id="da5fe-128">最初のメソッドでは、コンテナーのマッピングでは、各ビューのエントリを使って、ディクショナリを作成します。</span><span class="sxs-lookup"><span data-stu-id="da5fe-128">The first method creates a dictionary with an entry for each view in the container mapping.</span></span> <span data-ttu-id="da5fe-129">2 番目のメソッドでは、1 つのコンテナーのマッピングのハッシュ値を計算し、実行時の検証に使用、モデルが、ビューが事前に生成した後に変更していないこと。</span><span class="sxs-lookup"><span data-stu-id="da5fe-129">The second method computes a hash value for the single container mapping and is used at runtime to validate that the model has not changed since the views were pre-generated.</span></span> <span data-ttu-id="da5fe-130">2 つのメソッドのオーバーライドは、複数のコンテナーのマッピングに関連する複雑なシナリオで提供されます。</span><span class="sxs-lookup"><span data-stu-id="da5fe-130">Overrides of the two methods are provided for complex scenarios involving multiple container mappings.</span></span>

<span data-ttu-id="da5fe-131">ビューを生成するときに GenerateViews メソッドを呼び出すし、結果として得られる EntitySetBase と DbMappingView を記述します。</span><span class="sxs-lookup"><span data-stu-id="da5fe-131">When generating views you will call the GenerateViews method and then write out the resulting EntitySetBase and DbMappingView.</span></span> <span data-ttu-id="da5fe-132">また、ComputeMappingHashValue メソッドによって生成されたハッシュを格納する必要があります。</span><span class="sxs-lookup"><span data-stu-id="da5fe-132">You will also need to store the hash generated by the ComputeMappingHashValue method.</span></span>

### <a name="loading-views"></a><span data-ttu-id="da5fe-133">ビューの読み込み</span><span class="sxs-lookup"><span data-stu-id="da5fe-133">Loading Views</span></span>

<span data-ttu-id="da5fe-134">GenerateViews メソッドによって生成されるビューを読み込むためには、DbMappingViewCache 抽象クラスから継承するクラスを使用して EF を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="da5fe-134">In order to load the views generated by the GenerateViews method, you can provide EF with a class that inherits from the DbMappingViewCache abstract class.</span></span> <span data-ttu-id="da5fe-135">DbMappingViewCache は、2 つのメソッドを実装する必要がありますを指定します。</span><span class="sxs-lookup"><span data-stu-id="da5fe-135">DbMappingViewCache specifies two methods that you must implement:</span></span>

``` csharp
    public abstract string MappingHashValue { get; }
    public abstract DbMappingView GetView(EntitySetBase extent);
```

<span data-ttu-id="da5fe-136">MappingHashValue プロパティには、ComputeMappingHashValue メソッドによって生成されたハッシュを返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="da5fe-136">The MappingHashValue property must return the hash generated by the ComputeMappingHashValue method.</span></span> <span data-ttu-id="da5fe-137">EF がビューの確認にはまず生成し、モデルのハッシュ値をこのプロパティによって返されるハッシュと比較します。</span><span class="sxs-lookup"><span data-stu-id="da5fe-137">When EF is going to ask for views it will first generate and compare the hash value of the model with the hash returned by this property.</span></span> <span data-ttu-id="da5fe-138">これらが一致しない場合は、EF は EntityCommandCompilationException 例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="da5fe-138">If they do not match then EF will throw an EntityCommandCompilationException exception.</span></span>

<span data-ttu-id="da5fe-139">GetView メソッドで、EntitySetBase はそのまま使用し、GenerateViews メソッドによって生成されたディクショナリで指定された EntitySetBase に関連付けられていた解析対象の EntitySql が生成する対象を含む DbMappingVIew を返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="da5fe-139">The GetView method will accept an EntitySetBase and you need to return a DbMappingVIew containing the EntitySql that was generated for that was associated with the given EntitySetBase in the dictionary generated by the GenerateViews method.</span></span> <span data-ttu-id="da5fe-140">EF を求める場合ことがない、GetView ビューは null を返します。</span><span class="sxs-lookup"><span data-stu-id="da5fe-140">If EF asks for a view that you do not have then GetView should return null.</span></span>

<span data-ttu-id="da5fe-141">格納および取得のために必要な解析対象の EntitySql 1 つの方法がわかりますが、上記とパワー ツールで生成される DbMappingViewCache からの抜粋を次に示します。</span><span class="sxs-lookup"><span data-stu-id="da5fe-141">The following is an extract from the DbMappingViewCache that is generated with the Power Tools as described above, in it we see one way to store and retrieve the EntitySql required.</span></span>

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

<span data-ttu-id="da5fe-142">EF に使用しているには、追加する、DbMappingViewCache は用に作成されたコンテキストの指定、DbMappingViewCacheTypeAttribute を使用します。</span><span class="sxs-lookup"><span data-stu-id="da5fe-142">To have EF use your DbMappingViewCache you add use the DbMappingViewCacheTypeAttribute, specifying the context that it was created for.</span></span> <span data-ttu-id="da5fe-143">次のコードで MyMappingViewCache クラスを使用して、BlogContext を関連付けます。</span><span class="sxs-lookup"><span data-stu-id="da5fe-143">In the code below we associate the BlogContext with the MyMappingViewCache class.</span></span>

``` csharp
    [assembly: DbMappingViewCacheType(typeof(BlogContext), typeof(MyMappingViewCache))]
```

<span data-ttu-id="da5fe-144">複雑なシナリオは、マッピング ビュー キャッシュ ファクトリを指定することによってマッピング キャッシュ インスタンスの表示を提供することができます。</span><span class="sxs-lookup"><span data-stu-id="da5fe-144">For more complex scenarios, mapping view cache instances can be provided by specifying a mapping view cache factory.</span></span> <span data-ttu-id="da5fe-145">これは、抽象クラス System.Data.Entity.Infrastructure.MappingViews.DbMappingViewCacheFactory を実装することで実行できます。</span><span class="sxs-lookup"><span data-stu-id="da5fe-145">This can be done by implementing the abstract class System.Data.Entity.Infrastructure.MappingViews.DbMappingViewCacheFactory.</span></span> <span data-ttu-id="da5fe-146">使用されるマッピング ビュー キャッシュ ファクトリのインスタンスを取得または、StorageMappingItemCollection.MappingViewCacheFactoryproperty を使用して設定できます。</span><span class="sxs-lookup"><span data-stu-id="da5fe-146">The instance of the mapping view cache factory that is used can be retrieved or set using the StorageMappingItemCollection.MappingViewCacheFactoryproperty.</span></span>
