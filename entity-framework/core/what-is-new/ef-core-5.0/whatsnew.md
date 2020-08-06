---
title: EF Core 5.0 の新機能
description: EF Core 5.0 の新機能の概要
author: ajcvickers
ms.date: 07/20/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: d7f5863e657e243ce733eda5dc8b40c1b92818ce
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526876"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="53c8b-103">EF Core 5.0 の新機能</span><span class="sxs-lookup"><span data-stu-id="53c8b-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="53c8b-104">EF Core 5.0 は現在開発中です。</span><span class="sxs-lookup"><span data-stu-id="53c8b-104">EF Core 5.0 is currently in development.</span></span> <span data-ttu-id="53c8b-105">このページには、各プレビューで導入された耳寄りな変更の概要が記載されています。</span><span class="sxs-lookup"><span data-stu-id="53c8b-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="53c8b-106">このページには [EF Core 5.0 のプラン](xref:core/what-is-new/ef-core-5.0/plan)を記載していません。</span><span class="sxs-lookup"><span data-stu-id="53c8b-106">This page does not duplicate the [plan for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span></span> <span data-ttu-id="53c8b-107">このプランでは、最終リリースの出荷前に含めようとしているものすべてを含めた、EF Core 5.0 のテーマ全体について説明します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="53c8b-108">公開されている公式ドキュメントについては、リンクが追加されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-7"></a><span data-ttu-id="53c8b-109">Preview 7</span><span class="sxs-lookup"><span data-stu-id="53c8b-109">Preview 7</span></span>

### <a name="dbcontextfactory"></a><span data-ttu-id="53c8b-110">DbContextFactory</span><span class="sxs-lookup"><span data-stu-id="53c8b-110">DbContextFactory</span></span>

<span data-ttu-id="53c8b-111">EF Core 5.0 には、アプリケーションの依存関係挿入 (D.I.) コンテナーに DbContext インスタンスを作成するファクトリを登録する `AddDbContextFactory` と `AddPooledDbContextFactory` が導入されています。</span><span class="sxs-lookup"><span data-stu-id="53c8b-111">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container.</span></span> <span data-ttu-id="53c8b-112">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-112">For example:</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="53c8b-113">これにより、ASP.NET Core コントローラーなどのアプリケーション サービスが、サービス コンストラクターの `IDbContextFactory<TContext>` に依存できます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-113">Application services such as ASP.NET Core controllers can then depend on `IDbContextFactory<TContext>` in the service constructor.</span></span> <span data-ttu-id="53c8b-114">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-114">For example:</span></span>

```csharp
public class MyController
{
    private readonly IDbContextFactory<SomeDbContext> _contextFactory;

    public MyController(IDbContextFactory<SomeDbContext> contextFactory)
    {
        _contextFactory = contextFactory;
    }
}
```

<span data-ttu-id="53c8b-115">これにより、DbContext インスタンスを必要に応じて作成および使用できます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-115">DbContext instances can then be created and used as needed.</span></span> <span data-ttu-id="53c8b-116">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-116">For example:</span></span>

```csharp
public void DoSomeThing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...            
    }
}
```

<span data-ttu-id="53c8b-117">なお、この方法で作成された DbContext インスタンスは、アプリケーションのサービス プロバイダーには管理 "_されず_"、従ってアプリケーションによって破棄される必要があります。</span><span class="sxs-lookup"><span data-stu-id="53c8b-117">Note that the DbContext instances created in this way are _not_ managed by the application's service provider and therefore must be disposed by the application.</span></span> <span data-ttu-id="53c8b-118">この分離は、`IDbContextFactory` の使用が推奨される Blazor アプリケーションで非常に便利ですが、他のシナリオでも役立つ場合があります。</span><span class="sxs-lookup"><span data-stu-id="53c8b-118">This decoupling is very useful for Blazor applications, where using `IDbContextFactory` is recommended, but may also be useful in other scenarios.</span></span>

<span data-ttu-id="53c8b-119">DbContext インスタンスは、`AddPooledDbContextFactory` を呼び出すことによってプールできます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-119">DbContext instances can be pooled by calling `AddPooledDbContextFactory`.</span></span> <span data-ttu-id="53c8b-120">このプールは、`AddDbContextPool` の場合と同じように動作しますが、同じ制限もあります。</span><span class="sxs-lookup"><span data-stu-id="53c8b-120">This pooling works the same way as for `AddDbContextPool`, and also has the same limitations.</span></span>

<span data-ttu-id="53c8b-121">ドキュメントは、イシュー [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="53c8b-121">Documentation is tracked by issue [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span></span>

### <a name="reset-dbcontext-state"></a><span data-ttu-id="53c8b-122">DbContext の状態のリセット</span><span class="sxs-lookup"><span data-stu-id="53c8b-122">Reset DbContext state</span></span>

<span data-ttu-id="53c8b-123">EF Core 5.0 には、追跡対象のすべてのエンティティの DbContext をクリアする `ChangeTracker.Clear()` が導入されています。</span><span class="sxs-lookup"><span data-stu-id="53c8b-123">EF Core 5.0 introduces `ChangeTracker.Clear()` which clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="53c8b-124">これは、作業単位ごとに有効期間が短い新しいコンテキスト インスタンスを作成するベスト プラクティスを使用している場合には通常不要です。</span><span class="sxs-lookup"><span data-stu-id="53c8b-124">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="53c8b-125">ただし、DbContext インスタンスの状態をリセットする必要がある場合、この新しい `Clear()` メソッドを使用すると、すべてのエンティティを一括でデタッチするよりも、パフォーマンスと堅牢性が向上します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-125">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more performant and robust than mass-detaching all entities.</span></span>  

<span data-ttu-id="53c8b-126">ドキュメントは、イシュー [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="53c8b-126">Documentation is tracked by issue [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span></span>

### <a name="new-pattern-for-store-generated-defaults"></a><span data-ttu-id="53c8b-127">ストアで生成された既定値に新しいパターン</span><span class="sxs-lookup"><span data-stu-id="53c8b-127">New pattern for store-generated defaults</span></span>

<span data-ttu-id="53c8b-128">EF Core では、既定の制約値がある列に、値を明示的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-128">EF Core allows an explicit value to be set for a column that may also have default value constraint.</span></span> <span data-ttu-id="53c8b-129">EF Core では、このセンチネルとして、プロパティ型に CLR の既定値を使用しています。つまり、その値が CLR の既定でない場合はそれが挿入され、それ以外の場合は、データベースの既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-129">EF Core uses the CLR default of type property type as a sentinel for this; if the value is not the CLR default, then it is inserted, otherwise the database default is used.</span></span>

<span data-ttu-id="53c8b-130">これは、既定の CLR が適切なセンチネルではない型 (特に、`bool` のプロパティ) で問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-130">This creates problems for types where the CLR default is not a good sentinel--most notably, `bool` properties.</span></span> <span data-ttu-id="53c8b-131">EF Core 5.0 で、次のようなケースで、バッキング フィールドに null 値が許容されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-131">EF Core 5.0 now allows the backing field to be nullable for cases like this.</span></span> <span data-ttu-id="53c8b-132">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-132">For example:</span></span>

```csharp
public class Blog
{
    private bool? _isValid;

    public bool IsValid
    {
        get => _isValid ?? false;
        set => _isValid = value;
    }
}
```

<span data-ttu-id="53c8b-133">なお、null 値はバッキング フィールドでは許容されますが、公開されているプロパティでは許容されません。</span><span class="sxs-lookup"><span data-stu-id="53c8b-133">Note that the backing field is nullable, but the publicly exposed property is not.</span></span> <span data-ttu-id="53c8b-134">これにより、エンティティ型の公開サーフェスに影響を与えることなく、センチネル値を `null` にできます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-134">This allows the sentinel value to be `null` without impacting the public surface of the entity type.</span></span> <span data-ttu-id="53c8b-135">この場合、`IsValid` が設定されない場合、バッキング フィールドは null のままであるため、データベースの既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-135">In this case, if the `IsValid` is never set, then the database default will be used since the backing field remains null.</span></span> <span data-ttu-id="53c8b-136">`true` または `false` のいずれかが設定される場合、この値はデータベースに明示的に保存されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-136">If either `true` or `false` are set, then this value is saved explicitly to the database.</span></span>

<span data-ttu-id="53c8b-137">ドキュメントは、イシュー [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="53c8b-137">Documentation is tracked by issue [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="53c8b-138">Cosmos のパーティション キー</span><span class="sxs-lookup"><span data-stu-id="53c8b-138">Cosmos partition keys</span></span>

<span data-ttu-id="53c8b-139">EF Core では、EF モデルに Cosmos パーティション キーを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-139">EF Core allows the Cosmos partition key is included in the EF model.</span></span> <span data-ttu-id="53c8b-140">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-140">For example:</span></span>

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

<span data-ttu-id="53c8b-141">Preview 7 以降では、一部のクエリのパフォーマンスの向上のために、パーティション キーがエンティティ型の PK に含まれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-141">Starting with preview 7, the partition key is included in the entity type's PK and is used to improved performance in some queries.</span></span>

<span data-ttu-id="53c8b-142">ドキュメントは、イシュー [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="53c8b-142">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="cosmos-configuration"></a><span data-ttu-id="53c8b-143">Cosmos の構成</span><span class="sxs-lookup"><span data-stu-id="53c8b-143">Cosmos configuration</span></span>

<span data-ttu-id="53c8b-144">EF Core 5.0 では、Cosmos と Cosmos への接続の構成が改善されています。</span><span class="sxs-lookup"><span data-stu-id="53c8b-144">EF Core 5.0 improves configuration of Cosmos and Cosmos connections.</span></span>

<span data-ttu-id="53c8b-145">以前は、EF Core を Cosmos データベースに接続する場合に、エンドポイントとキーを明示的に指定する必要がありました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-145">Previously, EF Core required the end-point and key to be specified explicitly when connecting to a Cosmos database.</span></span> <span data-ttu-id="53c8b-146">EF Core 5.0 では、代わりに接続文字列を使用できます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-146">EF Core 5.0 allows use of a connection string instead.</span></span> <span data-ttu-id="53c8b-147">また、EF Core 5.0 では、WebProxy インスタンスを明示的に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-147">In addition, EF Core 5.0 allows the WebProxy instance to be explicitly set.</span></span> <span data-ttu-id="53c8b-148">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-148">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

<span data-ttu-id="53c8b-149">その他、多くのタイムアウト値、制限なども構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-149">Many other timeout values, limits, etc. can now also be configured.</span></span> <span data-ttu-id="53c8b-150">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-150">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.LimitToEndpoint();
                cosmosOptionsBuilder.RequestTimeout(requestTimeout);
                cosmosOptionsBuilder.OpenTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.IdleTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.GatewayModeMaxConnectionLimit(connectionLimit);
                cosmosOptionsBuilder.MaxTcpConnectionsPerEndpoint(connectionLimit);
                cosmosOptionsBuilder.MaxRequestsPerTcpConnection(requestLimit);
            });
```

<span data-ttu-id="53c8b-151">最後に、一般により互換性がある、`ConnectionMode.Gateway` が既定の接続モードになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-151">Finally, the default connection mode is now `ConnectionMode.Gateway`, which is generally more compatible.</span></span>

<span data-ttu-id="53c8b-152">ドキュメントは、イシュー [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="53c8b-152">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="scaffold-dbcontext-now-singularizes"></a><span data-ttu-id="53c8b-153">DbContext のスキャフォールディングの単数化</span><span class="sxs-lookup"><span data-stu-id="53c8b-153">Scaffold-DbContext now singularizes</span></span>

<span data-ttu-id="53c8b-154">以前は EF Core で、既存のデータベースから DbContext をスキャフォールディングする場合、データベース内のテーブル名と一致するエンティティ型名が作成されていました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-154">Previously when scaffolding a DbContext from an existing database, EF Core will create entity type names that match the table names in the database.</span></span> <span data-ttu-id="53c8b-155">たとえば、`People` テーブルと `Addresses` テーブルには、`People` と `Addresses` という名前のエンティティ型が生成されていました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-155">For example, tables `People` and `Addresses` resulted in entity types named `People` and `Addresses`.</span></span>

<span data-ttu-id="53c8b-156">この動作は、以前のリリースでは、複数形化サービスの登録によって構成できました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-156">In previous releases, this behavior was configurable through registration of a pluralization service.</span></span> <span data-ttu-id="53c8b-157">EF Core 5.0 では、既定の複数形化サービスとして [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) パッケージが使用されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-157">Now in EF Core 5.0, the [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) package is used as a default pluralization service.</span></span> <span data-ttu-id="53c8b-158">つまり、`People` テーブルと `Addresses` テーブルは、`Person` と `Address` という名前のエンティティ型にリバース エンジニアリングされます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-158">This means tables `People` and `Addresses` will now be reverse engineered to entity types named `Person` and `Address`.</span></span>

### <a name="savepoints"></a><span data-ttu-id="53c8b-159">セーブポイント</span><span class="sxs-lookup"><span data-stu-id="53c8b-159">Savepoints</span></span>

<span data-ttu-id="53c8b-160">EF Core では、複数の操作を実行するトランザクションをより細かく制御する、[セーブポイント](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks)がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-160">EF Core now supports [savepoints](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks) for greater control over transactions that execute multiple operations.</span></span>

<span data-ttu-id="53c8b-161">セーブポイントは、手動で作成、解放、ロールバックすることができます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-161">Savepoints can be manually created, released, and rolled back.</span></span> <span data-ttu-id="53c8b-162">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-162">For example:</span></span>

```csharp
context.Database.CreateSavepoint("MySavePoint"); 
```

<span data-ttu-id="53c8b-163">また、`SaveChanges` の実行に失敗すると、EF Core では最後のセーブポイントにロールバックされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-163">In addition, EF Core will now roll back to the last savepoint when executing `SaveChanges` fails.</span></span> <span data-ttu-id="53c8b-164">これにより、トランザクション全体を再実行しなくても、SaveChanges を再実行できます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-164">This allows SaveChanges to be re-tried without re-trying the entire transaction.</span></span>

<span data-ttu-id="53c8b-165">ドキュメントは、イシュー [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429) で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="53c8b-165">Documentation is tracked by issue [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span></span>

## <a name="preview-6"></a><span data-ttu-id="53c8b-166">Preview 6</span><span class="sxs-lookup"><span data-stu-id="53c8b-166">Preview 6</span></span>

### <a name="split-queries-for-related-collections"></a><span data-ttu-id="53c8b-167">関連するコレクションのクエリを分割する</span><span class="sxs-lookup"><span data-stu-id="53c8b-167">Split queries for related collections</span></span>

<span data-ttu-id="53c8b-168">EF Core 3.0 以降、EF Core では、LINQ クエリごとに 1 つの SQL クエリが常に生成されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-168">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="53c8b-169">これにより、使用中のトランザクション モードの制約内で返されるデータの整合性が確保されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-169">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="53c8b-170">ただし、クエリで `Include` またはプロジェクションを使用して複数の関連コレクションを戻すと、この処理が非常に遅くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="53c8b-170">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="53c8b-171">EF Core 5.0 では、関連するコレクションを含む単一の LINQ クエリを複数の SQL クエリに分割できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-171">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="53c8b-172">これにより、パフォーマンスを大幅に向上させることができますが、2 つのクエリ間でデータが変更された場合に返される結果に不整合が生じる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="53c8b-172">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="53c8b-173">シリアル化可能な、またはスナップショットのトランザクションを使用すると、これを軽減し、分割されたクエリとの整合性を維持できますが、他のパフォーマンス コストと動作の違いが生じる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="53c8b-173">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

#### <a name="split-queries-with-include"></a><span data-ttu-id="53c8b-174">インクルードを使用してクエリを分割する</span><span class="sxs-lookup"><span data-stu-id="53c8b-174">Split queries with Include</span></span>

<span data-ttu-id="53c8b-175">たとえば、`Include` を使用して 2 つのレベルの関連コレクションを取得するクエリについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-175">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```CSharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="53c8b-176">既定では、SQLite プロバイダーの使用時に、EF Core によって次の SQL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-176">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name", "t0"."Id", "t0"."ArtistId", "t0"."Title", "t0"."Id0", "t0"."AlbumId", "t0"."Name"
FROM "Artists" AS "a"
LEFT JOIN (
    SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "t"."Id" AS "Id0", "t"."AlbumId", "t"."Name"
    FROM "Album" AS "a0"
    LEFT JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
) AS "t0" ON "a"."Id" = "t0"."ArtistId"
ORDER BY "a"."Id", "t0"."Id", "t0"."Id0"
```

<span data-ttu-id="53c8b-177">新しい `AsSplitQuery` API を使用して、この動作を変更できます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-177">The new `AsSplitQuery` API can be used to change this behavior.</span></span> <span data-ttu-id="53c8b-178">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-178">For example:</span></span>

```CSharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="53c8b-179">AsSplitQuery は、すべてのリレーショナル データベース プロバイダーで使用でき、AsNoTracking と同様に、クエリ内の任意の場所で使用できます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-179">AsSplitQuery is available for all relational database providers and can be used anywhere in the query, just like AsNoTracking.</span></span> <span data-ttu-id="53c8b-180">EF Core によって、次の 3 つの SQL クエリが生成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-180">EF Core will now generate the following three SQL queries:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id", "a0"."Id"

SELECT "t"."Id", "t"."AlbumId", "t"."Name", "a"."Id", "a0"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
INNER JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
ORDER BY "a"."Id", "a0"."Id"
```

<span data-ttu-id="53c8b-181">クエリ ルートに対するすべての操作がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="53c8b-181">All operations on the query root are supported.</span></span> <span data-ttu-id="53c8b-182">これには、OrderBy/Skip/Take、結合操作、FirstOrDefault、および類似した単一結果選択操作が含まれます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-182">This includes OrderBy/Skip/Take, Join operations, FirstOrDefault and similar single result selecting operations.</span></span>

<span data-ttu-id="53c8b-183">OrderBy/Skip/Take でフィルター処理されたインクルードは、Preview 6 ではサポートされませんが、デイリー ビルドで使用でき、Preview 7 には含まれます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-183">Note that filtered Includes with OrderBy/Skip/Take are not supported in preview 6, but are available in the daily builds and will be included in preview 7.</span></span>

#### <a name="split-queries-with-collection-projections"></a><span data-ttu-id="53c8b-184">コレクション プロジェクションを使用してクエリを分割する</span><span class="sxs-lookup"><span data-stu-id="53c8b-184">Split queries with collection projections</span></span>

<span data-ttu-id="53c8b-185">`AsSplitQuery` は、コレクションがプロジェクションに読み込まれるときにも使用できます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-185">`AsSplitQuery` can also be used when collections are loaded in projections.</span></span> <span data-ttu-id="53c8b-186">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-186">For example:</span></span>

```CSharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

<span data-ttu-id="53c8b-187">この LINQ クエリでは、SQLite プロバイダーを使用するときに、次の 2 つの SQL クエリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-187">This LINQ query generates the following two SQL queries when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

<span data-ttu-id="53c8b-188">サポートされるのはコレクションの具体化のみであることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="53c8b-188">Note that only materialization of the collection is supported.</span></span> <span data-ttu-id="53c8b-189">上記のケースの `e.Albums` 後のコンポジションでは、分割クエリは発生しません。</span><span class="sxs-lookup"><span data-stu-id="53c8b-189">Any composition after `e.Albums` in above case won't result in a split query.</span></span> <span data-ttu-id="53c8b-190">この領域の機能強化は、[#21234](https://github.com/dotnet/efcore/issues/21234) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-190">Improvements in this area are tracked by [#21234](https://github.com/dotnet/efcore/issues/21234).</span></span>

### <a name="indexattribute"></a><span data-ttu-id="53c8b-191">IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="53c8b-191">IndexAttribute</span></span>

<span data-ttu-id="53c8b-192">新しい IndexAttribute をエンティティ型に配置して、1 つの列のインデックスを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-192">The new IndexAttribute can be placed on an entity type to specify an index for a single column.</span></span> <span data-ttu-id="53c8b-193">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-193">For example:</span></span>

```CSharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }
    
    [MaxLength(128)]
    public string FullName { get; set; }
}
```

<span data-ttu-id="53c8b-194">SQL Server の場合、移行によって次の SQL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-194">For SQL Server, Migrations will then generate the following SQL:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

<span data-ttu-id="53c8b-195">IndexAttribute を使用して、複数の列にまたがるインデックスを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-195">IndexAttribute can also be used to specify an index spanning multiple columns.</span></span> <span data-ttu-id="53c8b-196">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-196">For example:</span></span>

```CSharp
[Index(nameof(FirstName), nameof(LastName), IsUnique = true)]
public class User
{
    public int Id { get; set; }
    
    [MaxLength(64)]
    public string FirstName { get; set; }

    [MaxLength(64)]
    public string LastName { get; set; }
}
```

<span data-ttu-id="53c8b-197">SQL Server の場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="53c8b-197">For SQL Server, this results in:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

<span data-ttu-id="53c8b-198">ドキュメントは、イシュー [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-198">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="improved-query-translation-exceptions"></a><span data-ttu-id="53c8b-199">クエリ変換の例外の改善</span><span class="sxs-lookup"><span data-stu-id="53c8b-199">Improved query translation exceptions</span></span>

<span data-ttu-id="53c8b-200">クエリ変換が失敗したときに生成される例外メッセージの改善を続けています。</span><span class="sxs-lookup"><span data-stu-id="53c8b-200">We are continuing to improve the exception messages generated when query translation fails.</span></span> <span data-ttu-id="53c8b-201">たとえば、次のクエリでは、マップされていないプロパティ `IsSigned` を使用します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-201">For example, this query uses the unmapped property `IsSigned`:</span></span>

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

<span data-ttu-id="53c8b-202">EF Core は、`IsSigned` がマップされていないために変換が失敗したことを示す次の例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="53c8b-202">EF Core will throw the following exception indicating that translation failed because `IsSigned` is not mapped:</span></span>

> <span data-ttu-id="53c8b-203">ハンドルされていない例外です。</span><span class="sxs-lookup"><span data-stu-id="53c8b-203">Unhandled exception.</span></span> <span data-ttu-id="53c8b-204">System.InvalidOperationException:LINQ 式 'DbSet<Artist>() .Where(a => a.IsSigned)' を変換できませんでした。</span><span class="sxs-lookup"><span data-stu-id="53c8b-204">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.IsSigned)' could not be translated.</span></span> <span data-ttu-id="53c8b-205">追加情報:エンティティ型 'Artist' のメンバー 'IsSigned' の変換に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-205">Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed.</span></span> <span data-ttu-id="53c8b-206">指定されたメンバーがマップされていない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="53c8b-206">Possibly the specified member is not mapped.</span></span> <span data-ttu-id="53c8b-207">変換できる形式でクエリを書き直すか、AsEnumerable()、AsAsyncEnumerable()、ToList()、または ToListAsync() のいずれかの呼び出しを挿入して、クライアント評価に明示的に切り替えてください。</span><span class="sxs-lookup"><span data-stu-id="53c8b-207">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="53c8b-208">詳細については、「 https://go.microsoft.com/fwlink/?linkid=2101038 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53c8b-208">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

<span data-ttu-id="53c8b-209">同様に、カルチャに依存するセマンティクスを使用して文字列の比較を変換しようとしたときに、より適切な例外メッセージが生成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-209">Similarly, better exception messages are now generated when attempting to translate string comparisons with culture-dependent semantics.</span></span> <span data-ttu-id="53c8b-210">たとえば、次のクエリは `StringComparison.CurrentCulture` を使用しようとします。</span><span class="sxs-lookup"><span data-stu-id="53c8b-210">For example, this query attempts to use `StringComparison.CurrentCulture`:</span></span>

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

<span data-ttu-id="53c8b-211">EF Core では、次の例外がスローされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-211">EF Core will now throw the following exception:</span></span>

> <span data-ttu-id="53c8b-212">ハンドルされていない例外です。</span><span class="sxs-lookup"><span data-stu-id="53c8b-212">Unhandled exception.</span></span> <span data-ttu-id="53c8b-213">System.InvalidOperationException:LINQ 式 'DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))' を変換できませんでした。</span><span class="sxs-lookup"><span data-stu-id="53c8b-213">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))' could not be translated.</span></span> <span data-ttu-id="53c8b-214">追加情報:'StringComparison' 引数を受け取る 'string.Equals' メソッドの変換はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="53c8b-214">Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported.</span></span> <span data-ttu-id="53c8b-215">詳細については、「 https://go.microsoft.com/fwlink/?linkid=2129535 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53c8b-215">See https://go.microsoft.com/fwlink/?linkid=2129535 for more information.</span></span> <span data-ttu-id="53c8b-216">変換できる形式でクエリを書き直すか、AsEnumerable()、AsAsyncEnumerable()、ToList()、または ToListAsync() のいずれかの呼び出しを挿入して、クライアント評価に明示的に切り替えてください。</span><span class="sxs-lookup"><span data-stu-id="53c8b-216">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="53c8b-217">詳細については、「 https://go.microsoft.com/fwlink/?linkid=2101038 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53c8b-217">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

### <a name="specify-transaction-id"></a><span data-ttu-id="53c8b-218">トランザクション ID を指定する</span><span class="sxs-lookup"><span data-stu-id="53c8b-218">Specify transaction ID</span></span>

<span data-ttu-id="53c8b-219">この機能は、[@Marusyk](https://github.com/Marusyk) によってコミュニティから提供されました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-219">This feature was contributed from the community by [@Marusyk](https://github.com/Marusyk).</span></span> <span data-ttu-id="53c8b-220">投稿に感謝します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-220">Many thanks for the contribution!</span></span>

<span data-ttu-id="53c8b-221">EF Core では、呼び出し全体のトランザクションの相関関係に対するトランザクション ID が公開されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-221">EF Core exposes a transaction ID for correlation of transactions across calls.</span></span> <span data-ttu-id="53c8b-222">この ID は通常、トランザクションの開始時に EF Core によって設定されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-222">This ID typically set by EF Core when a transaction is started.</span></span> <span data-ttu-id="53c8b-223">アプリケーションがトランザクションを代わりに開始する場合、この機能により、アプリケーションが、使用されるすべての場所で正しく関連付けられるように、トランザクション ID を明示的に設定できるようになります。</span><span class="sxs-lookup"><span data-stu-id="53c8b-223">If the application starts the transaction instead, then this feature allows the application to explicitly set the transaction ID so it is correlated correctly everywhere it is used.</span></span> <span data-ttu-id="53c8b-224">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-224">For example:</span></span>

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a><span data-ttu-id="53c8b-225">IPAddress マッピング</span><span class="sxs-lookup"><span data-stu-id="53c8b-225">IPAddress mapping</span></span>

<span data-ttu-id="53c8b-226">この機能は、[@ralmsdeveloper](https://github.com/ralmsdeveloper) によってコミュニティから提供されました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-226">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="53c8b-227">投稿に感謝します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-227">Many thanks for the contribution!</span></span>

<span data-ttu-id="53c8b-228">標準の .NET [IPAddress クラス](/dotnet/api/system.net.ipaddress)が、ネイティブ サポートをまだ持っていないデータベースの文字列型の列に自動的にマップされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-228">The standard .NET [IPAddress class](/dotnet/api/system.net.ipaddress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="53c8b-229">たとえば、次のエンティティ型をマップすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="53c8b-229">For example, consider mapping this entity type:</span></span>

```CSharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

<span data-ttu-id="53c8b-230">SQL Server では、移行によって次のテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-230">On SQL Server, this will result in Migrations creating the following table:</span></span>

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
``` 

<span data-ttu-id="53c8b-231">これで、通常の方法でエンティティを追加できます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-231">Entities can then be added in the normal way:</span></span>

```CSharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
``` 

<span data-ttu-id="53c8b-232">結果として生成される SQL では、正規化された IPv4 または IPv6 のアドレスが挿入されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-232">And the resulting SQL will insert the normalized IPv4 or IPv6 address:</span></span>

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a><span data-ttu-id="53c8b-233">スキャフォールディング時の OnConfiguring を除外する</span><span class="sxs-lookup"><span data-stu-id="53c8b-233">Exclude OnConfiguring when scaffolding</span></span>

<span data-ttu-id="53c8b-234">DbContext が既存のデータベースからスキャフォールディングされると、EF Core の既定では、接続文字列を使用して OnConfiguring オーバーロードが作成され、コンテキストがすぐに使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="53c8b-234">When a DbContext is scaffolded from an existing database, EF Core by default creates an OnConfiguring overload with a connection string so that the context is immediately usable.</span></span> <span data-ttu-id="53c8b-235">ただし、これは、OnConfiguring を持つ部分クラスが既に存在する場合や、コンテキストを他の方法で構成する場合には役に立ちません。</span><span class="sxs-lookup"><span data-stu-id="53c8b-235">However, this is not useful if you already have a partial class with OnConfiguring, or if you are configuring the context some other way.</span></span>

<span data-ttu-id="53c8b-236">これに対処するため、スキャフォールディング コマンドに、OnConfiguring の生成を省略するように指示できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-236">To address this, the scaffolding commands can now be instructed to omit generation of OnConfiguring.</span></span> <span data-ttu-id="53c8b-237">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-237">For example:</span></span>

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

<span data-ttu-id="53c8b-238">または、パッケージ マネージャー コンソールで:</span><span class="sxs-lookup"><span data-stu-id="53c8b-238">Or in the Package Manager Console:</span></span>

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring 
``` 

<span data-ttu-id="53c8b-239">[名前付き接続文字列と、ユーザー シークレットのようなセキュリティで保護されたストレージ](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets)を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="53c8b-239">Note that we recommend using [a named connection string and secure storage like User Secrets](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).</span></span>

### <a name="translations-for-firstordefault-on-strings"></a><span data-ttu-id="53c8b-240">文字列の FirstOrDefault の変換</span><span class="sxs-lookup"><span data-stu-id="53c8b-240">Translations for FirstOrDefault on strings</span></span>

<span data-ttu-id="53c8b-241">この機能は、[@dvoreckyaa](https://github.com/dvoreckyaa) によってコミュニティから提供されました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-241">This feature was contributed from the community by [@dvoreckyaa](https://github.com/dvoreckyaa).</span></span> <span data-ttu-id="53c8b-242">投稿に感謝します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-242">Many thanks for the contribution!</span></span>

<span data-ttu-id="53c8b-243">FirstOrDefault と文字列内の文字に対する類似の演算子が変換されました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-243">FirstOrDefault and similar operators for characters in strings are now translated.</span></span> <span data-ttu-id="53c8b-244">たとえば、次の LINQ クエリ:</span><span class="sxs-lookup"><span data-stu-id="53c8b-244">For example, this LINQ query:</span></span>

```CSharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

<span data-ttu-id="53c8b-245">は、SQL Server を使用すると、次の SQL に変換されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-245">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a><span data-ttu-id="53c8b-246">CASE ブロックの簡略化</span><span class="sxs-lookup"><span data-stu-id="53c8b-246">Simplify case blocks</span></span>

<span data-ttu-id="53c8b-247">EF Core は、CASE ブロックを使用してより良いクエリを生成するようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-247">EF Core now generates better queries with CASE blocks.</span></span> <span data-ttu-id="53c8b-248">たとえば、次の LINQ クエリ:</span><span class="sxs-lookup"><span data-stu-id="53c8b-248">For example, this LINQ query:</span></span> 

```CSharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

<span data-ttu-id="53c8b-249">は、SQL Server では、正式には次のように変換されていました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-249">Was on SQL Server formally translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN (CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END = 0) AND CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
``` 

<span data-ttu-id="53c8b-250">しかし、今後は次のように変換されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-250">But is now translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
``` 

## <a name="preview-5"></a><span data-ttu-id="53c8b-251">Preview 5</span><span class="sxs-lookup"><span data-stu-id="53c8b-251">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="53c8b-252">データベースの照合順序</span><span class="sxs-lookup"><span data-stu-id="53c8b-252">Database collations</span></span>

<span data-ttu-id="53c8b-253">データベースにおける既定の照合順序を EF モデルで指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-253">The default collation for a database can now be specified in the EF model.</span></span> <span data-ttu-id="53c8b-254">これは、データベースの作成時に照合順序を設定するために生成された移行にフローします。</span><span class="sxs-lookup"><span data-stu-id="53c8b-254">This will flow through to generated migrations to set the collation when the database is created.</span></span> <span data-ttu-id="53c8b-255">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-255">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="53c8b-256">次に、移行では、SQL Server 上にデータベースを作成するために次のものが生成されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-256">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="53c8b-257">特定のデータベース列に使用する照合順序を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-257">The collation to use for specific database columns can also be specified.</span></span> <span data-ttu-id="53c8b-258">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-258">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="53c8b-259">移行を使用しないものについては、DbContext をスキャフォールディングする際に、照合順序がデータベースからリバースエンジニアリングされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-259">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="53c8b-260">最後に、`EF.Functions.Collate()` では、さまざまな照合順序を使用したアドホック クエリが可能です。</span><span class="sxs-lookup"><span data-stu-id="53c8b-260">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span> <span data-ttu-id="53c8b-261">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-261">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="53c8b-262">これにより、SQL Server に対して次のクエリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-262">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="53c8b-263">アドホック照合順序はデータベースのパフォーマンスに悪影響を及ぼす可能性があるため、注意して使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="53c8b-263">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="53c8b-264">ドキュメントは、イシュー [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-264">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="53c8b-265">引数を IDesignTimeDbContextFactory にフローする</span><span class="sxs-lookup"><span data-stu-id="53c8b-265">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="53c8b-266">引数は、コマンド ラインから [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1)の `CreateDbContext` メソッドにフローされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-266">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span></span> <span data-ttu-id="53c8b-267">たとえば、これが開発ビルドであることを示すために、カスタム引数 (`dev` など) をコマンド ラインで渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-267">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can be passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
``` 

<span data-ttu-id="53c8b-268">この引数は次にファクトリにフローされ、そこではコンテキストの作成方法および初期化方法を制御するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-268">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span> <span data-ttu-id="53c8b-269">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-269">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args) 
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="53c8b-270">ドキュメントは、イシュー [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-270">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="53c8b-271">識別子の解決を使用した追跡なしのクエリ</span><span class="sxs-lookup"><span data-stu-id="53c8b-271">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="53c8b-272">識別子の解決を実行するように追跡なしのクエリ構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-272">No-tracking queries can now be configured to perform identity resolution.</span></span> <span data-ttu-id="53c8b-273">たとえば、次のクエリでは、各ブログの主キーが同じである場合でも、投稿ごとに新しいブログ インスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-273">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span> 

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="53c8b-274">ただし、通常は速度が少し低下し、常により多くのメモリを使用することになりますが、1 つのブログ インスタンスだけを確実に作成するように、このクエリを変更することができます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-274">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="53c8b-275">これは、追跡なしのクエリに対してのみ有効です。すべての追跡クエリでは既にこの動作が行われているからです。</span><span class="sxs-lookup"><span data-stu-id="53c8b-275">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="53c8b-276">また、API レビューに従って、`PerformIdentityResolution` 構文も変更されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-276">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span> <span data-ttu-id="53c8b-277">[#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53c8b-277">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="53c8b-278">ドキュメントは、イシュー [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-278">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="53c8b-279">格納された (保存された) 計算列</span><span class="sxs-lookup"><span data-stu-id="53c8b-279">Stored (persisted) computed columns</span></span>

<span data-ttu-id="53c8b-280">ほとんどのデータベースでは、計算後に計算列の値を格納することができます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-280">Most databases allow computed column values to be stored after computation.</span></span> <span data-ttu-id="53c8b-281">これによってディスク領域が消費されますが、計算列の計算は、その値が取得されるたびではなく、更新時に 1 回だけ行われます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-281">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span> <span data-ttu-id="53c8b-282">また、これにより、一部のデータベースについて列のインデックスを作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-282">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="53c8b-283">EF Core 5.0 では、計算列を格納済みとして構成できます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-283">EF Core 5.0 allows computed columns to be configured as stored.</span></span> <span data-ttu-id="53c8b-284">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-284">For example:</span></span>
 
```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="53c8b-285">SQLite の計算列</span><span class="sxs-lookup"><span data-stu-id="53c8b-285">SQLite computed columns</span></span>

<span data-ttu-id="53c8b-286">EF Core では、SQLite データベースの計算列がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-286">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="53c8b-287">Preview 4</span><span class="sxs-lookup"><span data-stu-id="53c8b-287">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="53c8b-288">モデルでデータベースの有効桁数または小数点以下桁数を構成する</span><span class="sxs-lookup"><span data-stu-id="53c8b-288">Configure database precision/scale in model</span></span>

<span data-ttu-id="53c8b-289">モデル ビルダーを使用して、プロパティの有効桁数と小数点以下桁数を指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-289">Precision and scale for a property can now be specified using the model builder.</span></span> <span data-ttu-id="53c8b-290">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-290">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="53c8b-291">有効桁数と小数点以下桁数は、"decimal(16,4)" のように、完全なデータベースの種類を使用して設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-291">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span> 

<span data-ttu-id="53c8b-292">ドキュメントは、イシュー [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-292">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="53c8b-293">SQL Server インデックスの指定 FILL FACTOR を指定する</span><span class="sxs-lookup"><span data-stu-id="53c8b-293">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="53c8b-294">SQL Server でインデックスを作成するときに、FILL FACTOR を指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-294">The fill factor can now be specified when creating an index on SQL Server.</span></span> <span data-ttu-id="53c8b-295">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-295">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="53c8b-296">Preview 3</span><span class="sxs-lookup"><span data-stu-id="53c8b-296">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="53c8b-297">フィルター処理されたインクルード</span><span class="sxs-lookup"><span data-stu-id="53c8b-297">Filtered Include</span></span>

<span data-ttu-id="53c8b-298">Include メソッドでは、インクルードされるエンティティのフィルター処理がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-298">The Include method now supports filtering of the entities included.</span></span> <span data-ttu-id="53c8b-299">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-299">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="53c8b-300">このクエリでは、投稿のタイトルに "チーズ" が含まれている場合にのみ、関連付けられている各投稿と共にブログが返されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-300">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="53c8b-301">Skip と Take を使用して、インクルードされるエンティティの数を減らすこともできます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-301">Skip and Take can also be used to reduce the number of included entities.</span></span> <span data-ttu-id="53c8b-302">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-302">For example:</span></span>
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="53c8b-303">このクエリでは、ブログ 1 件あたり最大 5 件の投稿が含まれるブログが返されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-303">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="53c8b-304">詳細については、[インクルードに関するドキュメント](xref:core/querying/related-data#filtered-include)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53c8b-304">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="53c8b-305">ナビゲーション プロパティの新しい ModelBuilder API</span><span class="sxs-lookup"><span data-stu-id="53c8b-305">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="53c8b-306">ナビゲーション プロパティは、主に[リレーションシップを定義する](xref:core/modeling/relationships)ときに構成されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-306">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span> <span data-ttu-id="53c8b-307">ただし、ナビゲーション プロパティに追加の構成が必要な場合は、新しい `Navigation` メソッドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-307">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span> <span data-ttu-id="53c8b-308">たとえば、フィールドが規約によって見つからない場合にナビゲーションにバッキング フィールドを設定するには、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-308">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="53c8b-309">`Navigation` API は、リレーションシップの構成に代わるものではないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="53c8b-309">Note that the `Navigation` API does not replace relationship configuration.</span></span> <span data-ttu-id="53c8b-310">代わりに、既に検出または定義されたリレーションシップで、ナビゲーション プロパティの追加構成を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-310">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="53c8b-311">[ナビゲーション プロパティの構成に関するドキュメント](xref:core/modeling/relationships#configuring-navigation-properties)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53c8b-311">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="53c8b-312">名前空間と接続文字列の新しいコマンドライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="53c8b-312">New command-line parameters for namespaces and connection strings</span></span> 

<span data-ttu-id="53c8b-313">移行とスキャフォールディングでは、コマンド ラインで名前空間を指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-313">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span> <span data-ttu-id="53c8b-314">たとえば、別の名前空間にコンテキスト クラスとモデル クラスを配置するデータベースをリバース エンジニアリングするには、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-314">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span> 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="53c8b-315">詳細については、「[移行](xref:core/managing-schemas/migrations/index#namespaces)」と「[リバース エンジニアリング](xref:core/managing-schemas/scaffolding#directories-and-namespaces)」のドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="53c8b-315">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="53c8b-316">また、接続文字列を `database-update` コマンドに渡すことができるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-316">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="53c8b-317">詳細については、[ツールのドキュメント](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53c8b-317">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="53c8b-318">VS パッケージ マネージャー コンソールで使用される PowerShell コマンドにも、同等のパラメーターが追加されています。</span><span class="sxs-lookup"><span data-stu-id="53c8b-318">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="53c8b-319">EnableDetailedErrors が返された</span><span class="sxs-lookup"><span data-stu-id="53c8b-319">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="53c8b-320">パフォーマンス上の理由から、EF では、データベースから値を読み取るときに追加の null チェックは行われません。</span><span class="sxs-lookup"><span data-stu-id="53c8b-320">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span> <span data-ttu-id="53c8b-321">これにより、予期しない null が検出された場合に、根本原因を突き止めることが困難な例外が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="53c8b-321">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="53c8b-322">`EnableDetailedErrors` を使用すると、クエリに null チェックがさらに追加されます。パフォーマンスのオーバーヘッドが小さいため、これらのエラーでは、根本原因まで簡単に追跡できるようになります。</span><span class="sxs-lookup"><span data-stu-id="53c8b-322">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>  

<span data-ttu-id="53c8b-323">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-323">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="53c8b-324">ドキュメントは、イシュー [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-324">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="53c8b-325">Cosmos のパーティション キー</span><span class="sxs-lookup"><span data-stu-id="53c8b-325">Cosmos partition keys</span></span>

<span data-ttu-id="53c8b-326">指定されたクエリに使用するパーティション キーをクエリで指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-326">The partition key to use for a given query can now be specified in the query.</span></span> <span data-ttu-id="53c8b-327">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-327">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="53c8b-328">ドキュメントは、イシュー [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-328">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="53c8b-329">SQL Server DATALENGTH 関数のサポート</span><span class="sxs-lookup"><span data-stu-id="53c8b-329">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="53c8b-330">これには、新しい `EF.Functions.DataLength` メソッドを使用してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-330">This can be accessed using the new `EF.Functions.DataLength` method.</span></span> <span data-ttu-id="53c8b-331">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-331">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a><span data-ttu-id="53c8b-332">Preview 2</span><span class="sxs-lookup"><span data-stu-id="53c8b-332">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="53c8b-333">C# 属性を利用し、プロパティ バッキング フィールドを指定する</span><span class="sxs-lookup"><span data-stu-id="53c8b-333">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="53c8b-334">C# 属性を利用し、プロパティのバッキング フィールドを指定できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-334">A C# attribute can now be used to specify the backing field for a property.</span></span> <span data-ttu-id="53c8b-335">この属性を利用することで、EF Core では、バッキング フィールドが自動的に検出できないときでさえ、通常のようにバッキング フィールドとの間で読み書きできます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-335">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span> <span data-ttu-id="53c8b-336">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-336">For example:</span></span>

```CSharp
public class Blog
{
    private string _mainTitle;

    public int Id { get; set; }

    [BackingField(nameof(_mainTitle))]
    public string Title
    {
        get => _mainTitle;
        set => _mainTitle = value;
    }
}
```

<span data-ttu-id="53c8b-337">ドキュメントは、イシュー [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-337">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="53c8b-338">完全な識別子マッピング</span><span class="sxs-lookup"><span data-stu-id="53c8b-338">Complete discriminator mapping</span></span>

<span data-ttu-id="53c8b-339">EF Core では、[継承階層の TPH マッピング](/ef/core/modeling/inheritance)のために識別子列が使用されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-339">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span> <span data-ttu-id="53c8b-340">識別子に利用できるすべての値が EF Core で認識されている限り、パフォーマンスを一部改善できます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-340">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span> <span data-ttu-id="53c8b-341">EF Core 5.0 にはこのような機能強化が実装されました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-341">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="53c8b-342">たとえば、EF Core の前バージョンでは、ある階層内のすべての型を返すクエリにこの SQL が常に生成されました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-342">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="53c8b-343">EF Core 5.0 では現在、完全な識別子マッピングが構成されたとき、次が生成されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-343">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="53c8b-344">プレビュー 3 以降、これが既定の動作になります。</span><span class="sxs-lookup"><span data-stu-id="53c8b-344">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="53c8b-345">Microsoft.Data.Sqlite のパフォーマンス向上</span><span class="sxs-lookup"><span data-stu-id="53c8b-345">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="53c8b-346">SQLIte でパフォーマンスが 2 点改善されました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-346">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="53c8b-347">GetBytes、GetChars、GetTextReader でバイナリ データや文字列データを読み出す作業が SqliteBlob とストリームを利用することで一層効率的になりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-347">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="53c8b-348">SqliteConnection の初期化がゆっくりになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-348">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="53c8b-349">これらの機能強化は ADO.NET Microsoft.Data.Sqlite プロバイダーにあり、そのため、EF Core の外でもパフォーマンスが改善されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-349">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="53c8b-350">Preview 1</span><span class="sxs-lookup"><span data-stu-id="53c8b-350">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="53c8b-351">シンプルなログ</span><span class="sxs-lookup"><span data-stu-id="53c8b-351">Simple logging</span></span>

<span data-ttu-id="53c8b-352">この機能により、EF6 の `Database.Log` に似た機能が追加されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-352">This feature adds functionality similar to `Database.Log` in EF6.</span></span> <span data-ttu-id="53c8b-353">つまり、外部のログ記録フレームワークを構成せずに、EF Core からログを簡単に取得できるようになります。</span><span class="sxs-lookup"><span data-stu-id="53c8b-353">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="53c8b-354">暫定版のドキュメントは、[2019 年 12 月 5 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="53c8b-354">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="53c8b-355">追加のドキュメントは、イシュー [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-355">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="53c8b-356">生成された SQL の取得方法がシンプルに</span><span class="sxs-lookup"><span data-stu-id="53c8b-356">Simple way to get generated SQL</span></span>

<span data-ttu-id="53c8b-357">EF Core 5.0 では、LINQ クエリの実行時に EF Core によって生成される SQL を返す `ToQueryString` 拡張メソッドが導入されています。</span><span class="sxs-lookup"><span data-stu-id="53c8b-357">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="53c8b-358">暫定版ドキュメントは、[2020 年 1 月 9 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="53c8b-358">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="53c8b-359">追加のドキュメントは、イシュー [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-359">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="53c8b-360">エンティティにキーがないことを示すために C# 属性を使用</span><span class="sxs-lookup"><span data-stu-id="53c8b-360">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="53c8b-361">新しい `KeylessAttribute` を使用したキーを持たないようにエンティティ タイプを構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-361">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span> <span data-ttu-id="53c8b-362">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-362">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="53c8b-363">ドキュメントは、イシュー [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-363">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="53c8b-364">初期化された DbContext で接続または接続文字列が変更可能に</span><span class="sxs-lookup"><span data-stu-id="53c8b-364">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="53c8b-365">接続や接続文字列を使用しなくても、DbContext インスタンスを簡単に作成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-365">It is now easier to create a DbContext instance without any connection or connection string.</span></span> <span data-ttu-id="53c8b-366">また、コンテキスト インスタンスで接続または接続文字列をミュートできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-366">Also, the connection or connection string can now be mutated on the context instance.</span></span> <span data-ttu-id="53c8b-367">この機能により、同じコンテキスト インスタンスを異なるデータベースに動的に接続できるようになります。</span><span class="sxs-lookup"><span data-stu-id="53c8b-367">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="53c8b-368">ドキュメントは、イシュー [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-368">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="53c8b-369">変更追跡のプロキシ</span><span class="sxs-lookup"><span data-stu-id="53c8b-369">Change-tracking proxies</span></span>

<span data-ttu-id="53c8b-370">EF Core で、[INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) および [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1) を自動的に実装するランタイム プロキシを生成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-370">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span> <span data-ttu-id="53c8b-371">これにより、エンティティ プロパティの値の変更が EF Core に直接報告されるため、変更をスキャンする必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="53c8b-371">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span> <span data-ttu-id="53c8b-372">ただし、プロキシには独自の制限のセットが付属しているため、すべてのユーザーが使用できるわけではありません</span><span class="sxs-lookup"><span data-stu-id="53c8b-372">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="53c8b-373">ドキュメントは、イシュー [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-373">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="53c8b-374">強化されたデバッグ ビュー</span><span class="sxs-lookup"><span data-stu-id="53c8b-374">Enhanced debug views</span></span>

<span data-ttu-id="53c8b-375">デバッグ ビューで、イシューのデバッグ時に EF Core の内部を簡単に確認できます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-375">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span> <span data-ttu-id="53c8b-376">モデルのデバッグ ビューは少し前に実装されました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-376">A debug view for the Model was implemented some time ago.</span></span> <span data-ttu-id="53c8b-377">EF Core 5.0 では、モデル ビューを読みやすくし、状態マネージャーの追跡対象エンティティの新しいデバッグ ビューを追加しました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-377">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="53c8b-378">暫定版のドキュメントは、[2019 年 12 月 12 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="53c8b-378">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="53c8b-379">追加のドキュメントは、イシュー [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-379">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="53c8b-380">データベースの null セマンティクスの処理の向上</span><span class="sxs-lookup"><span data-stu-id="53c8b-380">Improved handling of database null semantics</span></span>

<span data-ttu-id="53c8b-381">リレーショナル データベースは通常、NULL を不明な値として扱うため、他の NULL とは等しくありません。</span><span class="sxs-lookup"><span data-stu-id="53c8b-381">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span> <span data-ttu-id="53c8b-382">C# では、null は、他の null と等しいかどうかを比較する定義済みの値として扱われます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-382">While C# treats null as a defined value, which compares equal to any other null.</span></span> <span data-ttu-id="53c8b-383">EF Core は、既定で C# の null セマンティクスを使用できるようにクエリを変換します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-383">EF Core by default translates queries so that they use C# null semantics.</span></span> <span data-ttu-id="53c8b-384">EF Core 5.0 では、この変換の効率が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-384">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="53c8b-385">ドキュメントは、イシュー [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-385">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="53c8b-386">インデクサーのプロパティ</span><span class="sxs-lookup"><span data-stu-id="53c8b-386">Indexer properties</span></span>

<span data-ttu-id="53c8b-387">EF Core 5.0 では、C# インデクサー プロパティのマッピングがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="53c8b-387">EF Core 5.0 supports mapping of C# indexer properties.</span></span> <span data-ttu-id="53c8b-388">これらのプロパティにより、エンティティはプロパティ バッグとして機能し、列がバッグの名前付きプロパティにマップされます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-388">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="53c8b-389">ドキュメントは、イシュー [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-389">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="53c8b-390">列挙型マッピングの CHECK 制約の生成</span><span class="sxs-lookup"><span data-stu-id="53c8b-390">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="53c8b-391">EF Core 5.0 の移行で、列挙型プロパティのマッピングに CHECK 制約を生成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-391">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span> <span data-ttu-id="53c8b-392">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-392">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="53c8b-393">ドキュメントは、イシュー [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-393">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="53c8b-394">IsRelational</span><span class="sxs-lookup"><span data-stu-id="53c8b-394">IsRelational</span></span>

<span data-ttu-id="53c8b-395">既存の `IsSqlServer`、`IsSqlite`、`IsInMemory` に加えて、新しい `IsRelational` メソッドが追加されました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-395">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span> <span data-ttu-id="53c8b-396">このメソッドは、DbContext がリレーショナル データベース プロバイダーを使用しているかどうかをテストするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-396">This method can be used to test if the DbContext is using any relational database provider.</span></span> <span data-ttu-id="53c8b-397">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-397">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="53c8b-398">ドキュメントは、イシュー [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-398">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="53c8b-399">ETag を使用した Cosmos オプティミスティック同時実行制御</span><span class="sxs-lookup"><span data-stu-id="53c8b-399">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="53c8b-400">Azure Cosmos DB データベース プロバイダーで、Etag を使用したオプティミスティック同時実行制御がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-400">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span> <span data-ttu-id="53c8b-401">OnModelCreating のモデル ビルダーを使用して ETag を構成します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-401">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="53c8b-402">SaveChanges はコンカレンシーの競合に対して `DbUpdateConcurrencyException` をスローします。これを[処理して](/ef/core/saving/concurrency)、たとえば再試行を実装することができます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-402">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="53c8b-403">ドキュメントは、イシュー [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-403">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="53c8b-404">より多くの DateTime コンストラクトに対するクエリ変換</span><span class="sxs-lookup"><span data-stu-id="53c8b-404">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="53c8b-405">新しい DateTime のコンストラクトが含まれるクエリを変換できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-405">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="53c8b-406">さらに、次の SQL Server 関数がマップされました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-406">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="53c8b-407">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="53c8b-407">DateDiffWeek</span></span>
* <span data-ttu-id="53c8b-408">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="53c8b-408">DateFromParts</span></span>

<span data-ttu-id="53c8b-409">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-409">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="53c8b-410">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-410">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="53c8b-411">より多くのバイト配列コンストラクトに対するクエリ変換</span><span class="sxs-lookup"><span data-stu-id="53c8b-411">Query translations for more byte array constructs</span></span>

<span data-ttu-id="53c8b-412">byte[] プロパティで Contains、Length、SequenceEqual などを使用するクエリが SQL に変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-412">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="53c8b-413">暫定版のドキュメントは、[2019 年 12 月 5 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="53c8b-413">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="53c8b-414">追加のドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-414">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="53c8b-415">Reverse のクエリ変換</span><span class="sxs-lookup"><span data-stu-id="53c8b-415">Query translation for Reverse</span></span>

<span data-ttu-id="53c8b-416">`Reverse` を使用したクエリが変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-416">Queries using `Reverse` are now translated.</span></span> <span data-ttu-id="53c8b-417">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="53c8b-417">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="53c8b-418">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-418">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="53c8b-419">ビット処理演算子のクエリ変換</span><span class="sxs-lookup"><span data-stu-id="53c8b-419">Query translation for bitwise operators</span></span>

<span data-ttu-id="53c8b-420">ビット処理演算子を使用するクエリが、次のようなケースでも変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-420">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="53c8b-421">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-421">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="53c8b-422">Cosmos の文字列のクエリ変換</span><span class="sxs-lookup"><span data-stu-id="53c8b-422">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="53c8b-423">文字列メソッド Contains、StartsWith、EndsWith を使用するクエリが、Azure Cosmos DB プロバイダーの使用時に変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="53c8b-423">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="53c8b-424">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="53c8b-424">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
