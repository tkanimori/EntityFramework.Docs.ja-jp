---
title: "読み込みに関連したデータの EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
ms.technology: entity-framework-core
uid: core/querying/related-data
ms.openlocfilehash: ec69bb128890a1e0b72fe77014f37747585bb5a5
ms.sourcegitcommit: 3b21a7fdeddc7b3c70d9b7777b72bef61f59216c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2018
---
# <a name="loading-related-data"></a><span data-ttu-id="9422c-102">関連データの読み込み</span><span class="sxs-lookup"><span data-stu-id="9422c-102">Loading Related Data</span></span>

<span data-ttu-id="9422c-103">Entity Framework Core では、関連エンティティの読み込みをモデルで、ナビゲーション プロパティを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="9422c-103">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="9422c-104">関連するデータの読み込みに使用される 3 つの一般的な O/RM パターンがあります。</span><span class="sxs-lookup"><span data-stu-id="9422c-104">There are three common O/RM patterns used to load related data.</span></span>
* <span data-ttu-id="9422c-105">**一括読み込み**最初のクエリの一部として、データベースから、関連するデータが読み込まれたことを意味します。</span><span class="sxs-lookup"><span data-stu-id="9422c-105">**Eager loading** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="9422c-106">**明示的な読み込み**は後で、データベースから、関連するデータを明示的に読み込むことを意味します。</span><span class="sxs-lookup"><span data-stu-id="9422c-106">**Explicit loading** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="9422c-107">**遅延読み込み**ナビゲーション プロパティにアクセスする場合、関連するデータが透過的にデータベースから読み込むことを意味します。</span><span class="sxs-lookup"><span data-stu-id="9422c-107">**Lazy loading** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span> <span data-ttu-id="9422c-108">遅延読み込みは、まだ EF Core では可能ではありません。</span><span class="sxs-lookup"><span data-stu-id="9422c-108">Lazy loading is not yet possible with EF Core.</span></span>

> [!TIP]  
> <span data-ttu-id="9422c-109">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="9422c-109">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="9422c-110">一括読み込み</span><span class="sxs-lookup"><span data-stu-id="9422c-110">Eager loading</span></span>

<span data-ttu-id="9422c-111">使用することができます、`Include`クエリの結果に含まれる関連データを指定します。</span><span class="sxs-lookup"><span data-stu-id="9422c-111">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="9422c-112">次の例では、ブログの結果に返される必要があります、`Posts`プロパティとして、関連する投稿に設定されます。</span><span class="sxs-lookup"><span data-stu-id="9422c-112">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> <span data-ttu-id="9422c-113">Entity Framework Core は自動的に修正をするナビゲーション プロパティをコンテキストのインスタンスに以前に読み込まれたその他のエンティティです。</span><span class="sxs-lookup"><span data-stu-id="9422c-113">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="9422c-114">場合でも、明示的には、ナビゲーション プロパティのデータを含まない、いくつかの場合は、プロパティを作成してか、以前に読み込まれたすべての関連するエンティティ。</span><span class="sxs-lookup"><span data-stu-id="9422c-114">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>


<span data-ttu-id="9422c-115">単一のクエリでは、複数のリレーションシップから関連するデータを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="9422c-115">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a><span data-ttu-id="9422c-116">複数のレベルを含む</span><span class="sxs-lookup"><span data-stu-id="9422c-116">Including multiple levels</span></span>

<span data-ttu-id="9422c-117">使用して関連するデータの複数のレベルを含むへのリレーションシップを通じてドリルダウンすることができます、`ThenInclude`メソッドです。</span><span class="sxs-lookup"><span data-stu-id="9422c-117">You can drill down thru relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="9422c-118">次の例では、すべてのブログの関連する投稿を各投稿の作成者を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="9422c-118">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#SingleThenInclude)]

> [!NOTE]  
> <span data-ttu-id="9422c-119">Visual Studio の現在のバージョンが不適切なコードの入力候補オプションを提供を正しい式を使用する場合に、構文エラーが発生フラグが付けられますが、`ThenInclude`メソッド コレクション ナビゲーション プロパティ。</span><span class="sxs-lookup"><span data-stu-id="9422c-119">Current versions of Visual Studio offer incorrect code completion options and can cause correct expressions to be flagged with syntax errors when using the `ThenInclude` method after a collection navigation property.</span></span> <span data-ttu-id="9422c-120">これは、https://github.com/dotnet/roslyn/issues/8237 で追跡 IntelliSense バグが発生する可能性です。</span><span class="sxs-lookup"><span data-stu-id="9422c-120">This is a symptom of an IntelliSense bug tracked at https://github.com/dotnet/roslyn/issues/8237.</span></span> <span data-ttu-id="9422c-121">これらの見かけ上の構文エラーを無視するコードが正しいと正常にコンパイルできる限り安全です。</span><span class="sxs-lookup"><span data-stu-id="9422c-121">It is safe to ignore these spurious syntax errors as long as the code is correct and can be compiled successfully.</span></span> 

<span data-ttu-id="9422c-122">複数の呼び出しをチェーンする`ThenInclude`関連データのレベルを含めてさらに続行します。</span><span class="sxs-lookup"><span data-stu-id="9422c-122">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

<span data-ttu-id="9422c-123">これを同じクエリで複数のレベルと複数のルートから関連するデータを含めるにはすべてを組み合わせることができます。</span><span class="sxs-lookup"><span data-stu-id="9422c-123">You can combine all of this to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#IncludeTree)]

<span data-ttu-id="9422c-124">いずれかが含まれるエンティティの複数の関連エンティティを追加することがあります。</span><span class="sxs-lookup"><span data-stu-id="9422c-124">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="9422c-125">たとえば、クエリを実行するときに`Blog`が含まれています、`Posts`両方を格納して、`Author`と`Tags`の`Posts`です。</span><span class="sxs-lookup"><span data-stu-id="9422c-125">For example, when querying `Blog`s, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="9422c-126">これを行うには、ルートからのパスを含めるそれぞれを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9422c-126">To do this, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="9422c-127">たとえば、`Blog -> Posts -> Author`と`Blog -> Posts -> Tags`です。</span><span class="sxs-lookup"><span data-stu-id="9422c-127">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="9422c-128">表示される冗長な結合、EF の統合はほとんどの場合、結合 SQL を生成するときにありません。</span><span class="sxs-lookup"><span data-stu-id="9422c-128">This does not mean you will get redundant joins, in most cases EF will consolidate the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

### <a name="ignored-includes"></a><span data-ttu-id="9422c-129">無視が含まれています</span><span class="sxs-lookup"><span data-stu-id="9422c-129">Ignored includes</span></span>

<span data-ttu-id="9422c-130">不要になったクエリの開始とエンティティ型のインスタンスを返すように、クエリを変更する場合は、include 演算子は無視されます。</span><span class="sxs-lookup"><span data-stu-id="9422c-130">If you change the query so that it no longer returns instances of the entity type that the query began with, then the include operators are ignored.</span></span>

<span data-ttu-id="9422c-131">次の例では、include 演算子に基づいて、 `Blog`、ところが、`Select`演算子を使用して、匿名型を取得するクエリを変更します。</span><span class="sxs-lookup"><span data-stu-id="9422c-131">In the following example, the include operators are based on the `Blog`, but then the `Select` operator is used to change the query to return an anonymous type.</span></span> <span data-ttu-id="9422c-132">この場合、include 演算子がある影響しません。</span><span class="sxs-lookup"><span data-stu-id="9422c-132">In this case, the include operators have no effect.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#IgnoredInclude)]

<span data-ttu-id="9422c-133">既定では、EF コアが、警告をログとは、演算子は無視されます。</span><span class="sxs-lookup"><span data-stu-id="9422c-133">By default, EF Core will log a warning when include operators are ignored.</span></span> <span data-ttu-id="9422c-134">参照してください[ログ](../miscellaneous/logging.md)ログ出力を表示する方法の詳細についてです。</span><span class="sxs-lookup"><span data-stu-id="9422c-134">See [Logging](../miscellaneous/logging.md) for more information on viewing logging output.</span></span> <span data-ttu-id="9422c-135">スローするか、何もする include 演算子が無視される動作を変更できます。</span><span class="sxs-lookup"><span data-stu-id="9422c-135">You can change the behavior when an include operator is ignored to either throw or do nothing.</span></span> <span data-ttu-id="9422c-136">では通常、コンテキストでのオプションを設定するときにこれは、 `DbContext.OnConfiguring`、または`Startup.cs`ASP.NET Core を使用している場合。</span><span class="sxs-lookup"><span data-stu-id="9422c-136">This is done when setting up the options for your context - typically in `DbContext.OnConfiguring`, or in `Startup.cs` if you are using ASP.NET Core.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/ThrowOnIgnoredInclude/BloggingContext.cs#OnConfiguring)]

## <a name="explicit-loading"></a><span data-ttu-id="9422c-137">明示的読み込み</span><span class="sxs-lookup"><span data-stu-id="9422c-137">Explicit loading</span></span>

> [!NOTE]  
> <span data-ttu-id="9422c-138">この機能は、EF コア 1.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="9422c-138">This feature was introduced in EF Core 1.1.</span></span>

<span data-ttu-id="9422c-139">使用して、ナビゲーション プロパティを明示的に読み込むことができます、 `DbContext.Entry(...)` API です。</span><span class="sxs-lookup"><span data-stu-id="9422c-139">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#Eager)]

<span data-ttu-id="9422c-140">ナビゲーション プロパティは、関連エンティティを返す別のクエリを実行することによっても明示的に読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="9422c-140">You can also explicitly load a navigation property by executing a seperate query that returns the related entities.</span></span> <span data-ttu-id="9422c-141">変更の追跡が有効になっているかどうか、EF コアのすべてのエンティティは既に読み込まれているを参照する新たに読み込まれたエンティティのナビゲーション プロパティを設定を参照してくださいに既に読み込まれているエンティティのナビゲーション プロパティを設定が自動的にエンティティを読み込むときに、新たに読み込まれたエンティティです。</span><span class="sxs-lookup"><span data-stu-id="9422c-141">If change tracking is enabled, then when loading an entity, EF Core will automatically set the navigation properties of the newly-loaded entitiy to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly-loaded entity.</span></span>

### <a name="querying-related-entities"></a><span data-ttu-id="9422c-142">関連エンティティのクエリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9422c-142">Querying related entities</span></span>

<span data-ttu-id="9422c-143">LINQ クエリを表すナビゲーション プロパティの内容を取得することもできます。</span><span class="sxs-lookup"><span data-stu-id="9422c-143">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="9422c-144">これにより、それらをメモリに読み込むことがなく、関連エンティティを aggregate 操作を実行しているなどの作業を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="9422c-144">This allows you to do things such as running an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

<span data-ttu-id="9422c-145">関連エンティティは、メモリに読み込まれるをフィルターすることもできます。</span><span class="sxs-lookup"><span data-stu-id="9422c-145">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a><span data-ttu-id="9422c-146">遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="9422c-146">Lazy loading</span></span>

<span data-ttu-id="9422c-147">遅延読み込みが EF のコアでまだサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="9422c-147">Lazy loading is not yet supported by EF Core.</span></span> <span data-ttu-id="9422c-148">表示することができます、[遅延読み込みバックログ項目に、](https://github.com/aspnet/EntityFramework/issues/3797)この機能を追跡するためにします。</span><span class="sxs-lookup"><span data-stu-id="9422c-148">You can view the [lazy loading item on our backlog](https://github.com/aspnet/EntityFramework/issues/3797) to track this feature.</span></span>

## <a name="related-data-and-serialization"></a><span data-ttu-id="9422c-149">関連データとシリアル化</span><span class="sxs-lookup"><span data-stu-id="9422c-149">Related data and serialization</span></span>

<span data-ttu-id="9422c-150">EF コアは自動的に修正をナビゲーション プロパティ、しまうサイクルで、オブジェクト グラフ内ためです。</span><span class="sxs-lookup"><span data-stu-id="9422c-150">Because EF Core will automatically fix-up navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="9422c-151">たとえば、ブログを読み込みが関連付けられて投稿投稿のコレクションを参照するブログ オブジェクトになります。</span><span class="sxs-lookup"><span data-stu-id="9422c-151">For example, Loading a blog and it's related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="9422c-152">これらの投稿の各ブログへの参照になります。</span><span class="sxs-lookup"><span data-stu-id="9422c-152">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="9422c-153">一部のシリアル化フレームワークでは、このようなサイクルは許可されません。</span><span class="sxs-lookup"><span data-stu-id="9422c-153">Some serialization frameworks do not allow such cycles.</span></span> <span data-ttu-id="9422c-154">たとえば、Json.NET であっても、循環参照が発生したときの場合、次の例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="9422c-154">For example, Json.NET will throw the following exception if a cycle is encoutered.</span></span>

> <span data-ttu-id="9422c-155">Newtonsoft.Json.JsonSerializationException: 自己のループを型 'MyApplication.Models.Blog' と 'ブログ' プロパティの検出を参照します。</span><span class="sxs-lookup"><span data-stu-id="9422c-155">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="9422c-156">ASP.NET Core を使用している場合は、オブジェクト グラフ内で見つかったサイクルを無視する Json.NET を構成できます。</span><span class="sxs-lookup"><span data-stu-id="9422c-156">If you are using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="9422c-157">これには、`ConfigureServices(...)`メソッド`Startup.cs`です。</span><span class="sxs-lookup"><span data-stu-id="9422c-157">This is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddMvc()
        .AddJsonOptions(
            options => options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore
        );

    ...
}
```
