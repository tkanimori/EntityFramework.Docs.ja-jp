---
title: Azure Cosmos DB プロバイダー - EF Core
description: Azure Cosmos DB SQL API と共に Entity Framework Core を使えるようにするデータベース プロバイダーに関するドキュメントです
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/index
ms.openlocfilehash: 6cac695288d9ba84968b7fab6361f55e9b51be67
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656091"
---
# <a name="ef-core-azure-cosmos-db-provider"></a><span data-ttu-id="00c17-103">EF Core Azure Cosmos DB プロバイダー</span><span class="sxs-lookup"><span data-stu-id="00c17-103">EF Core Azure Cosmos DB Provider</span></span>

>[!NOTE]
> <span data-ttu-id="00c17-104">このプロバイダーは、EF Core 3.0 で新しく追加されたものです。</span><span class="sxs-lookup"><span data-stu-id="00c17-104">This provider is new in EF Core 3.0.</span></span>

<span data-ttu-id="00c17-105">このデータベース プロバイダーにより、Azure Cosmos DB と共に Entity Framework Core を使用できます。</span><span class="sxs-lookup"><span data-stu-id="00c17-105">This database provider allows Entity Framework Core to be used with Azure Cosmos DB.</span></span> <span data-ttu-id="00c17-106">このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。</span><span class="sxs-lookup"><span data-stu-id="00c17-106">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

<span data-ttu-id="00c17-107">このセクションを読む前に、[Azure Cosmos DB のドキュメント](/azure/cosmos-db/introduction)を理解することを強くお勧めします。</span><span class="sxs-lookup"><span data-stu-id="00c17-107">It is strongly recommended to familiarize yourself with the [Azure Cosmos DB documentation](/azure/cosmos-db/introduction) before reading this section.</span></span>

>[!NOTE]
> <span data-ttu-id="00c17-108">このプロバイダーは、Azure Cosmos DB の SQL API でのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="00c17-108">This provider only works with the SQL API of Azure Cosmos DB.</span></span>

## <a name="install"></a><span data-ttu-id="00c17-109">インストール</span><span class="sxs-lookup"><span data-stu-id="00c17-109">Install</span></span>

<span data-ttu-id="00c17-110">[Microsoft.EntityFrameworkCore.Cosmos NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="00c17-110">Install the [Microsoft.EntityFrameworkCore.Cosmos NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/).</span></span>

## <a name="net-core-clitabdotnet-core-cli"></a>[<span data-ttu-id="00c17-111">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="00c17-111">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

``` console
dotnet add package Microsoft.EntityFrameworkCore.Cosmos
```

## <a name="visual-studiotabvs"></a>[<span data-ttu-id="00c17-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00c17-112">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Cosmos
```

***

## <a name="get-started"></a><span data-ttu-id="00c17-113">作業開始</span><span class="sxs-lookup"><span data-stu-id="00c17-113">Get started</span></span>

> [!TIP]  
> <span data-ttu-id="00c17-114">この記事の[サンプルは GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Cosmos) で確認できます。</span><span class="sxs-lookup"><span data-stu-id="00c17-114">You can view this article's [sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Cosmos).</span></span>

<span data-ttu-id="00c17-115">他のプロバイダーと同様に、最初の手順は [UseCosmos](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosDbContextOptionsExtensions.UseCosmos) を呼び出すことです。</span><span class="sxs-lookup"><span data-stu-id="00c17-115">Like for other providers the first step is to call [UseCosmos](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosDbContextOptionsExtensions.UseCosmos):</span></span>

[!code-csharp[Configuration](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Configuration)]

> [!WARNING]
> <span data-ttu-id="00c17-116">ここでは、わかりやすくするためにエンドポイントとキーをハードコーディングしていますが、運用アプリでは、これらは[安全に保存](/aspnet/core/security/app-secrets#secret-manager)する必要があります。</span><span class="sxs-lookup"><span data-stu-id="00c17-116">The endpoint and key are hardcoded here for simplicity, but in a production app these should be [stored securily](/aspnet/core/security/app-secrets#secret-manager)</span></span>

<span data-ttu-id="00c17-117">この例では、`Order` は、[所有されている型](../../modeling/owned-entities.md) `StreetAddress`への参照を持つ単純なエンティティです。</span><span class="sxs-lookup"><span data-stu-id="00c17-117">In this example `Order` is a simple entity with a reference to the [owned type](../../modeling/owned-entities.md) `StreetAddress`.</span></span>

[!code-csharp[Order](../../../../samples/core/Cosmos/ModelBuilding/Order.cs?name=Order)]

[!code-csharp[StreetAddress](../../../../samples/core/Cosmos/ModelBuilding/StreetAddress.cs?name=StreetAddress)]

<span data-ttu-id="00c17-118">データの保存とクエリは、通常の EF のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="00c17-118">Saving and quering data follows the normal EF pattern:</span></span>

[!code-csharp[HelloCosmos](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=HelloCosmos)]

> [!IMPORTANT]
> <span data-ttu-id="00c17-119">必須のコンテナーを作成し、モデル内に[シード データ](../../modeling/data-seeding.md)が存在する場合は挿入するようにするためには、[EnsureCreatedAsync](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreatedAsync) を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="00c17-119">Calling [EnsureCreatedAsync](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreatedAsync) is necessary to create the required containers and insert the [seed data](../../modeling/data-seeding.md) if present in the model.</span></span> <span data-ttu-id="00c17-120">ただし、`EnsureCreatedAsync` は、パフォーマンスの問題を引き起こす可能性があるため、通常の操作ではなく、配置時にのみ呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="00c17-120">However `EnsureCreatedAsync` should only be called during deployment, not normal operation, as it may cause performance issues.</span></span>

## <a name="cosmos-specific-model-customization"></a><span data-ttu-id="00c17-121">Cosmos 固有のモデルのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="00c17-121">Cosmos-specific model customization</span></span>

<span data-ttu-id="00c17-122">既定では、すべてのエンティティ型は、(この場合は `"OrderContext"` の) 派生コンテキストに基づいて命名された同じコンテナーにマップされます。</span><span class="sxs-lookup"><span data-stu-id="00c17-122">By default all entity types are mapped to the same container, named after the derived context (`"OrderContext"` in this case).</span></span> <span data-ttu-id="00c17-123">既定のコンテナー名を変更するには、[HasDefaultContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosModelBuilderExtensions.HasDefaultContainer) を使います。</span><span class="sxs-lookup"><span data-stu-id="00c17-123">To change the default container name use [HasDefaultContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosModelBuilderExtensions.HasDefaultContainer):</span></span>

[!code-csharp[DefaultContainer](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=DefaultContainer)]

<span data-ttu-id="00c17-124">エンティティ型を別のコンテナーにマップするには、[ToContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToContainer) を使います。</span><span class="sxs-lookup"><span data-stu-id="00c17-124">To map an entity type to a different container use [ToContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToContainer):</span></span>

[!code-csharp[Container](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Container)]

<span data-ttu-id="00c17-125">EF Core では、派生エンティティ型がない場合でも、特定の項目が表すエンティティ型の識別に識別子の値が追加されます。</span><span class="sxs-lookup"><span data-stu-id="00c17-125">To identify the entity type that a given item represent EF Core adds a discriminator value even if there are no derived entity types.</span></span> <span data-ttu-id="00c17-126">識別子の名前と値は[変更できます](../../modeling/inheritance.md)。</span><span class="sxs-lookup"><span data-stu-id="00c17-126">The name and value of the discriminator [can be changed](../../modeling/inheritance.md).</span></span>

<span data-ttu-id="00c17-127">他のエンティティ型が同じコンテナーに格納されることがない場合は、[HasNoDiscriminator](/dotnet/api/Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasNoDiscriminator) を呼び出すことで、その識別子を削除できます。</span><span class="sxs-lookup"><span data-stu-id="00c17-127">If no other entity type will ever be stored in the same container the discriminator can be removed by calling [HasNoDiscriminator](/dotnet/api/Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasNoDiscriminator):</span></span>

[!code-csharp[NoDiscriminator](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=NoDiscriminator)]

### <a name="partition-keys"></a><span data-ttu-id="00c17-128">パーティション キー</span><span class="sxs-lookup"><span data-stu-id="00c17-128">Partition keys</span></span>

<span data-ttu-id="00c17-129">既定では、EF Core ではパーティション キーが `"__partitionKey"` に設定されたコンテナーが作成されます。項目を挿入する際に、それに対して値が指定されることはありません。</span><span class="sxs-lookup"><span data-stu-id="00c17-129">By default EF Core will create containers with the partition key set to `"__partitionKey"` without supplying any value for it when inserting items.</span></span> <span data-ttu-id="00c17-130">しかし、Azure Cosmos のパフォーマンス機能を十分に活用するには、[慎重に選んだパーティション キー](/azure/cosmos-db/partition-data)を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="00c17-130">But to fully leverage the performance capabilities of Azure Cosmos a [carefully selected partition key](/azure/cosmos-db/partition-data) should be used.</span></span> <span data-ttu-id="00c17-131">これを構成するには、[HasPartitionKey](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.HasPartitionKey) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="00c17-131">It can be configured by calling [HasPartitionKey](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.HasPartitionKey):</span></span>

[!code-csharp[PartitionKey](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PartitionKey)]

>[!NOTE]
><span data-ttu-id="00c17-132">パーティション キーのプロパティは、それが[文字列に変換される](xref:core/modeling/value-conversions)限り、任意の型にすることができます。</span><span class="sxs-lookup"><span data-stu-id="00c17-132">The partition key property can be of any type as long as it is [converted to string](xref:core/modeling/value-conversions).</span></span>

<span data-ttu-id="00c17-133">一度構成したら、パーティション キーのプロパティは常に null 以外の値を持つ必要があります。</span><span class="sxs-lookup"><span data-stu-id="00c17-133">Once configured the partition key property should always have a non-null value.</span></span> <span data-ttu-id="00c17-134">クエリを発行するときに、条件を追加して単一パーティションにすることができます。</span><span class="sxs-lookup"><span data-stu-id="00c17-134">When issuing a query a condition can be added to make it single-partition.</span></span>

[!code-csharp[PartitionKey](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=PartitionKey)]

## <a name="embedded-entities"></a><span data-ttu-id="00c17-135">埋め込みエンティティ</span><span class="sxs-lookup"><span data-stu-id="00c17-135">Embedded entities</span></span>

<span data-ttu-id="00c17-136">Cosmos 所有エンティティは、所有者と同じアイテムに埋め込まれます。</span><span class="sxs-lookup"><span data-stu-id="00c17-136">For Cosmos owned entities are embedded in the same item as the owner.</span></span> <span data-ttu-id="00c17-137">プロパティ名を変更するには、[ToJsonProperty](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToJsonProperty) を使います。</span><span class="sxs-lookup"><span data-stu-id="00c17-137">To change a property name use [ToJsonProperty](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToJsonProperty):</span></span>

[!code-csharp[PropertyNames](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PropertyNames)]

<span data-ttu-id="00c17-138">この構成では、上記の例の順序は次のように格納されます。</span><span class="sxs-lookup"><span data-stu-id="00c17-138">With this configuration the order from the example above is stored like this:</span></span>

``` json
{
    "Id": 1,
    "PartitionKey": "1",
    "TrackingNumber": null,
    "id": "1",
    "Address": {
        "ShipsToCity": "London",
        "ShipsToStreet": "221 B Baker St"
    },
    "_rid": "6QEKAM+BOOABAAAAAAAAAA==",
    "_self": "dbs/6QEKAA==/colls/6QEKAM+BOOA=/docs/6QEKAM+BOOABAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683c-692e763901d5\"",
    "_attachments": "attachments/",
    "_ts": 1568163674
}
```

<span data-ttu-id="00c17-139">所有されているエンティティのコレクションも埋め込まれます。</span><span class="sxs-lookup"><span data-stu-id="00c17-139">Collections of owned entities are embedded as well.</span></span> <span data-ttu-id="00c17-140">次の例では、`Distributor` クラスを `StreetAddress` コレクションと共に使用します。</span><span class="sxs-lookup"><span data-stu-id="00c17-140">For the next example we'll use the `Distributor` class with a collection of `StreetAddress`:</span></span>

[!code-csharp[Distributor](../../../../samples/core/Cosmos/ModelBuilding/Distributor.cs?name=Distributor)]

<span data-ttu-id="00c17-141">所有されているエンティティは、明示的なキー値を格納する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="00c17-141">The owned entities don't need to provide explicit key values to be stored:</span></span>

[!code-csharp[OwnedCollection](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=OwnedCollection)]

<span data-ttu-id="00c17-142">これは、次のように永続化されます。</span><span class="sxs-lookup"><span data-stu-id="00c17-142">They will be persisted in this way:</span></span>

``` json
{
    "Id": 1,
    "Discriminator": "Distributor",
    "id": "Distributor|1",
    "ShippingCenters": [
        {
            "City": "Phoenix",
            "Street": "500 S 48th Street"
        },
        {
            "City": "Anaheim",
            "Street": "5650 Dolly Ave"
        }
    ],
    "_rid": "6QEKANzISj0BAAAAAAAAAA==",
    "_self": "dbs/6QEKAA==/colls/6QEKANzISj0=/docs/6QEKANzISj0BAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683c-7b2b439701d5\"",
    "_attachments": "attachments/",
    "_ts": 1568163705
}
```

<span data-ttu-id="00c17-143">EF Core には、追跡対象のすべてのエンティティに対して、内部で常に一意のキー値が必要です。</span><span class="sxs-lookup"><span data-stu-id="00c17-143">Internally EF Core always needs to have unique key values for all tracked entities.</span></span> <span data-ttu-id="00c17-144">所有されている型のコレクションに対して既定で作成される主キーは、所有者を指す外部キー プロパティと、JSON 配列内のインデックスに対応する `int` プロパティで構成されます。</span><span class="sxs-lookup"><span data-stu-id="00c17-144">The primary key created by default for collections of owned types consists of the foreign key properties pointing to the owner and an `int` property corresponding to the index in the JSON array.</span></span> <span data-ttu-id="00c17-145">これらの値を取得するには、エントリ API を使用します。</span><span class="sxs-lookup"><span data-stu-id="00c17-145">To retrieve these values entry API could be used:</span></span>

[!code-csharp[ImpliedProperties](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=ImpliedProperties)]

> [!TIP]
> <span data-ttu-id="00c17-146">必要に応じて、所有されているエンティティ型の既定の主キーは変更できますが、その場合、キー値は明示的に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="00c17-146">When necessary the default primary key for the owned entity types can be changed, but then key values should be provided explicitly.</span></span>

## <a name="working-with-disconnected-entities"></a><span data-ttu-id="00c17-147">接続解除エンティティの使用</span><span class="sxs-lookup"><span data-stu-id="00c17-147">Working with disconnected entities</span></span>

<span data-ttu-id="00c17-148">すべてのアイテムには、特定のパーティション キーに対して一意な `id` 値が必要です。</span><span class="sxs-lookup"><span data-stu-id="00c17-148">Every item needs to have an `id` value that is unique for the given partition key.</span></span> <span data-ttu-id="00c17-149">既定では、EF Core は、' | ' を区切り記号として使用して、識別子と主キーの値を連結して値を生成します。</span><span class="sxs-lookup"><span data-stu-id="00c17-149">By default EF Core generates the value by concatenating the discriminator and the primary key values, using '|' as a delimiter.</span></span> <span data-ttu-id="00c17-150">このキー値は、エンティティが `Added` 状態になったときにのみ生成されます。</span><span class="sxs-lookup"><span data-stu-id="00c17-150">The key values are only generated when an entity enters the `Added` state.</span></span> <span data-ttu-id="00c17-151">これは、.NET 型にその値を保存する `id` プロパティがない場合に[エンティティをアタッチするとき](../../saving/disconnected-entities.md)に問題になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="00c17-151">This might pose a problem when [attaching entities](../../saving/disconnected-entities.md) if they don't have an `id` property on the .NET type to store the value.</span></span>

<span data-ttu-id="00c17-152">この制限を回避するには、`id` 値を手動で作成して設定するか、エンティティをまず追加済みとしてマークして、その後目的の状態に変更します。</span><span class="sxs-lookup"><span data-stu-id="00c17-152">To work around this limitation one could create and set the `id` value manually or mark the entity as added first, then changing it to the desired state:</span></span>

[!code-csharp[Attach](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?highlight=4&name=Attach)]

<span data-ttu-id="00c17-153">結果の JSON は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="00c17-153">This is the resulting JSON:</span></span>

``` json
{
    "Id": 1,
    "Discriminator": "Distributor",
    "id": "Distributor|1",
    "ShippingCenters": [
        {
            "City": "Phoenix",
            "Street": "500 S 48th Street"
        }
    ],
    "_rid": "JBwtAN8oNYEBAAAAAAAAAA==",
    "_self": "dbs/JBwtAA==/colls/JBwtAN8oNYE=/docs/JBwtAN8oNYEBAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-9377-d7a1ae7c01d5\"",
    "_attachments": "attachments/",
    "_ts": 1572917100
}
```
