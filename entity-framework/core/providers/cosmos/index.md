---
title: Azure Cosmos DB プロバイダー - EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 09/12/2019
ms.assetid: 28264681-4486-4891-888c-be5e4ade24f1
uid: core/providers/cosmos/index
ms.openlocfilehash: 96686256bb93f5828bb21fed167eb57812806390
ms.sourcegitcommit: 6c28926a1e35e392b198a8729fc13c1c1968a27b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813545"
---
# <a name="ef-core-azure-cosmos-db-provider"></a><span data-ttu-id="a64dc-102">EF Core Azure Cosmos DB プロバイダー</span><span class="sxs-lookup"><span data-stu-id="a64dc-102">EF Core Azure Cosmos DB Provider</span></span>

>[!NOTE]
> <span data-ttu-id="a64dc-103">このプロバイダーは、EF Core 3.0 で新しく追加されたものです。</span><span class="sxs-lookup"><span data-stu-id="a64dc-103">This provider is new in EF Core 3.0.</span></span>

<span data-ttu-id="a64dc-104">このデータベース プロバイダーにより、Azure Cosmos DB と共に Entity Framework Core を使用できます。</span><span class="sxs-lookup"><span data-stu-id="a64dc-104">This database provider allows Entity Framework Core to be used with Azure Cosmos DB.</span></span> <span data-ttu-id="a64dc-105">このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。</span><span class="sxs-lookup"><span data-stu-id="a64dc-105">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

<span data-ttu-id="a64dc-106">このセクションを読む前に、[Azure Cosmos DB のドキュメント](https://docs.microsoft.com/en-us/azure/cosmos-db/introduction)を理解することを強くお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a64dc-106">It is strongly recommended to familiarize yourself with the [Azure Cosmos DB documentation](https://docs.microsoft.com/en-us/azure/cosmos-db/introduction) before reading this section.</span></span>

## <a name="install"></a><span data-ttu-id="a64dc-107">インストール</span><span class="sxs-lookup"><span data-stu-id="a64dc-107">Install</span></span>

<span data-ttu-id="a64dc-108">[Microsoft.EntityFrameworkCore.Cosmos NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="a64dc-108">Install the [Microsoft.EntityFrameworkCore.Cosmos NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/).</span></span>

# <a name="net-core-clitabdotnet-core-cli"></a>[<span data-ttu-id="a64dc-109">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a64dc-109">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

``` console
dotnet add package Microsoft.EntityFrameworkCore.Cosmos
```

# <a name="visual-studiotabvs"></a>[<span data-ttu-id="a64dc-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a64dc-110">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Cosmos
```

***

## <a name="get-started"></a><span data-ttu-id="a64dc-111">開始するには</span><span class="sxs-lookup"><span data-stu-id="a64dc-111">Get Started</span></span>

> [!TIP]  
> <span data-ttu-id="a64dc-112">この記事の[サンプルは GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Cosmos) で確認できます。</span><span class="sxs-lookup"><span data-stu-id="a64dc-112">You can view this article's [sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Cosmos).</span></span>

<span data-ttu-id="a64dc-113">他のプロバイダーと同様に、最初の手順は `UseCosmos` を呼び出すことです。</span><span class="sxs-lookup"><span data-stu-id="a64dc-113">Like for other providers the first step is to call `UseCosmos`:</span></span>

[!code-csharp[Configuration](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Configuration)]

> [!WARNING]
> <span data-ttu-id="a64dc-114">ここでは、わかりやすくするためにエンドポイントとキーをハードコーディングしていますが、運用アプリでは、これらは[安全に保存](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager)する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a64dc-114">The endpoint and key are hardcoded here for simplicity, but in a production app these should be [stored securily](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager)</span></span>

<span data-ttu-id="a64dc-115">この例では、`Order` は、[所有されている型](../../modeling/owned-entities.md) `StreetAddress`への参照を持つ単純なエンティティです。</span><span class="sxs-lookup"><span data-stu-id="a64dc-115">In this example `Order` is a simple entity with a reference to the [owned type](../../modeling/owned-entities.md) `StreetAddress`.</span></span>

[!code-csharp[Order](../../../../samples/core/Cosmos/ModelBuilding/Order.cs?name=Order)]

[!code-csharp[StreetAddress](../../../../samples/core/Cosmos/ModelBuilding/StreetAddress.cs?name=StreetAddress)]

<span data-ttu-id="a64dc-116">データの保存とクエリは、通常の EF のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="a64dc-116">Saving and quering data follows the normal EF pattern:</span></span>

[!code-csharp[HelloCosmos](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=HelloCosmos)]

> [!IMPORTANT]
> <span data-ttu-id="a64dc-117">必要なコレクションの作成と、モデルに存在する場合の[シード データ](../../modeling/data-seeding.md)の挿入には、`EnsureCreated` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="a64dc-117">Calling `EnsureCreated` is necessary to create the required collections and insert the [seed data](../../modeling/data-seeding.md) if present in the model.</span></span> <span data-ttu-id="a64dc-118">ただし、`EnsureCreated` は、パフォーマンスの問題を引き起こす可能性があるため、通常の操作ではなく、配置時にのみ呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="a64dc-118">However `EnsureCreated` should only be called during deployment, not normal operation, as it may cause performance issues.</span></span>

## <a name="cosmos-specific-model-customization"></a><span data-ttu-id="a64dc-119">Cosmos 固有のモデルのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="a64dc-119">Cosmos-specific Model Customization</span></span>

<span data-ttu-id="a64dc-120">既定では、すべてのエンティティ型は、(この場合は `"OrderContext"` の) 派生コンテキストに基づいて命名された同じコンテナーにマップされます。</span><span class="sxs-lookup"><span data-stu-id="a64dc-120">By default all entity types are mapped to the same container, named after the derived context (`"OrderContext"` in this case).</span></span> <span data-ttu-id="a64dc-121">既定のコンテナー名を変更するには、次のように `HasDefaultContainer` を使用します。</span><span class="sxs-lookup"><span data-stu-id="a64dc-121">To change the default container name use `HasDefaultContainer`:</span></span>

[!code-csharp[DefaultContainer](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=DefaultContainer)]

<span data-ttu-id="a64dc-122">エンティティ型を別のコンテナーにマップするには、次のように `ToContainer` を使用します。</span><span class="sxs-lookup"><span data-stu-id="a64dc-122">To map an entity type to a different container use `ToContainer`:</span></span>

[!code-csharp[Container](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Container)]

<span data-ttu-id="a64dc-123">EF Core では、派生エンティティ型がない場合でも、特定の項目が表すエンティティ型の識別に識別子の値が追加されます。</span><span class="sxs-lookup"><span data-stu-id="a64dc-123">To identify the entity type that a given item represent EF Core adds a discriminator value even if there are no derived entity types.</span></span> <span data-ttu-id="a64dc-124">識別子の名前と値は[変更できます](../../modeling/inheritance.md)。</span><span class="sxs-lookup"><span data-stu-id="a64dc-124">The name and value of the discriminator [can be changed](../../modeling/inheritance.md).</span></span>

## <a name="embedded-entities"></a><span data-ttu-id="a64dc-125">埋め込みエンティティ</span><span class="sxs-lookup"><span data-stu-id="a64dc-125">Embedded Entities</span></span>

<span data-ttu-id="a64dc-126">Cosmos 所有エンティティは、所有者と同じアイテムに埋め込まれます。</span><span class="sxs-lookup"><span data-stu-id="a64dc-126">For Cosmos owned entities are embedded in the same item as the owner.</span></span> <span data-ttu-id="a64dc-127">プロパティ名を変更するには、次のように `ToJsonProperty` を使用します。</span><span class="sxs-lookup"><span data-stu-id="a64dc-127">To change a property name use `ToJsonProperty`:</span></span>

[!code-csharp[PropertyNames](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PropertyNames)]

<span data-ttu-id="a64dc-128">この構成では、上記の例の順序は次のように格納されます。</span><span class="sxs-lookup"><span data-stu-id="a64dc-128">With this configuration the order from the example above is stored like this:</span></span>

``` json
{
    "Id": 1,
    "Discriminator": "Order",
    "TrackingNumber": null,
    "id": "Order|1",
    "Address": {
        "ShipsToCity": "London",
        "Discriminator": "StreetAddress",
        "ShipsToStreet": "221 B Baker St"
    },
    "_rid": "6QEKAM+BOOABAAAAAAAAAA==",
    "_self": "dbs/6QEKAA==/colls/6QEKAM+BOOA=/docs/6QEKAM+BOOABAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683c-692e763901d5\"",
    "_attachments": "attachments/",
    "_ts": 1568163674
}
```

<span data-ttu-id="a64dc-129">所有されているエンティティのコレクションも埋め込まれます。</span><span class="sxs-lookup"><span data-stu-id="a64dc-129">Collections of owned entities are embedded as well.</span></span> <span data-ttu-id="a64dc-130">次の例では、`Distributor` クラスを `StreetAddress` コレクションと共に使用します。</span><span class="sxs-lookup"><span data-stu-id="a64dc-130">For the next example we'll use the `Distributor` class with a collection of `StreetAddress`:</span></span>

[!code-csharp[Distributor](../../../../samples/core/Cosmos/ModelBuilding/Distributor.cs?name=Distributor)]

<span data-ttu-id="a64dc-131">所有されているエンティティは、明示的なキー値を格納する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="a64dc-131">The owned entities don't need to provide explicit key values to be stored:</span></span>

[!code-csharp[OwnedCollection](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=OwnedCollection)]

<span data-ttu-id="a64dc-132">これは、次のように永続化されます。</span><span class="sxs-lookup"><span data-stu-id="a64dc-132">They will be persisted in this way:</span></span>

``` json
{
    "Id": 1,
    "Discriminator": "Distributor",
    "id": "Distributor|1",
    "ShippingCenters": [
        {
            "City": "Phoenix",
            "Discriminator": "StreetAddress",
            "Street": "500 S 48th Street"
        },
        {
            "City": "Anaheim",
            "Discriminator": "StreetAddress",
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

<span data-ttu-id="a64dc-133">EF Core には、追跡対象のすべてのエンティティに対して、内部で常に一意のキー値が必要です。</span><span class="sxs-lookup"><span data-stu-id="a64dc-133">Internally EF Core always needs to have unique key values for all tracked entities.</span></span> <span data-ttu-id="a64dc-134">所有されている型のコレクションに対して既定で作成される主キーは、所有者を指す外部キー プロパティと、JSON 配列内のインデックスに対応する `int` プロパティで構成されます。</span><span class="sxs-lookup"><span data-stu-id="a64dc-134">The primary key created by default for collections of owned types consists of the foreign key properties pointing to the owner and an `int` property corresponding to the index in the JSON array.</span></span> <span data-ttu-id="a64dc-135">これらの値を取得するには、エントリ API を使用します。</span><span class="sxs-lookup"><span data-stu-id="a64dc-135">To retrieve these values entry API could be used:</span></span>

[!code-csharp[ImpliedProperties](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=ImpliedProperties)]

> [!TIP]
> <span data-ttu-id="a64dc-136">必要に応じて、所有されているエンティティ型の既定の主キーは変更できますが、その場合、キー値は明示的に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a64dc-136">When necessary the default primary key for the owned entity types can be changed, but then key values should be provided explicitly.</span></span>

## <a name="working-with-disconnected-entities"></a><span data-ttu-id="a64dc-137">接続解除エンティティの使用</span><span class="sxs-lookup"><span data-stu-id="a64dc-137">Working with Disconnected Entities</span></span>

<span data-ttu-id="a64dc-138">すべてのアイテムには、特定のパーティション キーに対して一意な `id` 値が必要です。</span><span class="sxs-lookup"><span data-stu-id="a64dc-138">Every item needs to have an `id` value that is unique for the given partition key.</span></span> <span data-ttu-id="a64dc-139">既定では、EF Core は、' | ' を区切り記号として使用して、識別子と主キーの値を連結して値を生成します。</span><span class="sxs-lookup"><span data-stu-id="a64dc-139">By default EF Core generates the value by concatenating the discriminator and the primary key values, using '|' as a delimiter.</span></span> <span data-ttu-id="a64dc-140">このキー値は、エンティティが `Added` 状態になったときにのみ生成されます。</span><span class="sxs-lookup"><span data-stu-id="a64dc-140">The key values are only generated when an entity enters the `Added` state.</span></span> <span data-ttu-id="a64dc-141">これは、.NET 型にその値を保存する `id` プロパティがない場合に[エンティティをアタッチするとき](../../saving/disconnected-entities.md)に問題になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="a64dc-141">This might pose a problem when [attaching entities](../../saving/disconnected-entities.md) if they don't have an `id` property on the .NET type to store the value.</span></span>

<span data-ttu-id="a64dc-142">この制限を回避するには、`id` 値を手動で作成して設定するか、エンティティをまず追加済みとしてマークして、その後目的の状態に変更します。</span><span class="sxs-lookup"><span data-stu-id="a64dc-142">To work around this limitation one could create and set the `id` value manually or mark the entity as added first, then changing it to the desired state:</span></span>

[!code-csharp[Attach](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?highlight=4&name=Attach)]

<span data-ttu-id="a64dc-143">結果の JSON は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="a64dc-143">This is the resulting JSON:</span></span>

``` json
{
    "Id": 1,
    "Discriminator": "Order",
    "TrackingNumber": null,
    "id": "Order|1",
    "Address": {
        "ShipsToCity": "London",
        "Discriminator": "StreetAddress",
        "ShipsToStreet": "3 Abbey Road"
    },
    "_rid": "6QEKAM+BOOABAAAAAAAAAA==",
    "_self": "dbs/6QEKAA==/colls/6QEKAM+BOOA=/docs/6QEKAM+BOOABAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683c-8f7ac48f01d5\"",
    "_attachments": "attachments/",
    "_ts": 1568163739
}
```
