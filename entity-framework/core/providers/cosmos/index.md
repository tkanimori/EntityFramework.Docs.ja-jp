---
title: Azure Cosmos DB プロバイダー - EF Core
description: Azure Cosmos DB SQL API と共に Entity Framework Core を使えるようにするデータベース プロバイダーに関するドキュメントです
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/index
ms.openlocfilehash: 6903aab4911f7478afe3d8987a791ae1c5ccebce
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502215"
---
# <a name="ef-core-azure-cosmos-db-provider"></a>EF Core Azure Cosmos DB プロバイダー

> [!NOTE]
> このプロバイダーは、EF Core 3.0 で新しく追加されたものです。

このデータベース プロバイダーにより、Azure Cosmos DB と共に Entity Framework Core を使用できます。 このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。

このセクションを読む前に、[Azure Cosmos DB のドキュメント](/azure/cosmos-db/introduction)を理解することを強くお勧めします。

> [!NOTE]
> このプロバイダーは、Azure Cosmos DB の SQL API でのみ機能します。

## <a name="install"></a>インストール

[Microsoft.EntityFrameworkCore.Cosmos NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/)をインストールします。

### <a name="net-core-clitabdotnet-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Cosmos
```

### <a name="visual-studiotabvs"></a>[Visual Studio](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Cosmos
```

***

## <a name="get-started"></a>作業開始

> [!TIP]  
> この記事の[サンプルは GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Cosmos) で確認できます。

他のプロバイダーと同様に、最初の手順は [UseCosmos](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosDbContextOptionsExtensions.UseCosmos) を呼び出すことです。

[!code-csharp[Configuration](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Configuration)]

> [!WARNING]
> ここでは、わかりやすくするためにエンドポイントとキーをハードコーディングしていますが、運用アプリでは、これらは[安全に保存](/aspnet/core/security/app-secrets#secret-manager)する必要があります。

この例では、`Order` は、[所有型](../../modeling/owned-entities.md) `StreetAddress` への参照を持つ単純なエンティティです。

[!code-csharp[Order](../../../../samples/core/Cosmos/ModelBuilding/Order.cs?name=Order)]

[!code-csharp[StreetAddress](../../../../samples/core/Cosmos/ModelBuilding/StreetAddress.cs?name=StreetAddress)]

データの保存とクエリは、通常の EF のパターンに従います。

[!code-csharp[HelloCosmos](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=HelloCosmos)]

> [!IMPORTANT]
> 必須のコンテナーを作成し、モデル内に[シード データ](../../modeling/data-seeding.md)が存在する場合は挿入するようにするためには、[EnsureCreatedAsync](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreatedAsync) を呼び出す必要があります。 ただし、`EnsureCreatedAsync` は、パフォーマンスの問題を引き起こす可能性があるため、通常の操作ではなく、配置時にのみ呼び出す必要があります。

## <a name="cosmos-specific-model-customization"></a>Cosmos 固有のモデルのカスタマイズ

既定では、すべてのエンティティ型は、(この場合は `"OrderContext"` の) 派生コンテキストに基づいて命名された同じコンテナーにマップされます。 既定のコンテナー名を変更するには、[HasDefaultContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosModelBuilderExtensions.HasDefaultContainer) を使います。

[!code-csharp[DefaultContainer](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=DefaultContainer)]

エンティティ型を別のコンテナーにマップするには、[ToContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToContainer) を使います。

[!code-csharp[Container](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Container)]

EF Core では、派生エンティティ型がない場合でも、特定の項目が表すエンティティ型の識別に識別子の値が追加されます。 識別子の名前と値は[変更できます](../../modeling/inheritance.md)。

他のエンティティ型が同じコンテナーに格納されることがない場合は、[HasNoDiscriminator](/dotnet/api/Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasNoDiscriminator) を呼び出すことで、その識別子を削除できます。

[!code-csharp[NoDiscriminator](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=NoDiscriminator)]

### <a name="partition-keys"></a>パーティション キー

既定では、EF Core ではパーティション キーが `"__partitionKey"` に設定されたコンテナーが作成されます。項目を挿入する際に、それに対して値が指定されることはありません。 しかし、Azure Cosmos のパフォーマンス機能を十分に活用するには、[慎重に選んだパーティション キー](/azure/cosmos-db/partition-data)を使用する必要があります。 これを構成するには、[HasPartitionKey](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.HasPartitionKey) を呼び出します。

[!code-csharp[PartitionKey](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PartitionKey)]

> [!NOTE]
>パーティション キーのプロパティは、それが[文字列に変換される](xref:core/modeling/value-conversions)限り、任意の型にすることができます。

一度構成したら、パーティション キーのプロパティは常に null 以外の値を持つ必要があります。 クエリを発行するときに、条件を追加して単一パーティションにすることができます。

[!code-csharp[PartitionKey](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=PartitionKey)]

## <a name="embedded-entities"></a>埋め込みエンティティ

Cosmos 所有エンティティは、所有者と同じアイテムに埋め込まれます。 プロパティ名を変更するには、[ToJsonProperty](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToJsonProperty) を使います。

[!code-csharp[PropertyNames](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PropertyNames)]

この構成では、上記の例の順序は次のように格納されます。

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

所有されているエンティティのコレクションも埋め込まれます。 次の例では、`Distributor` クラスを `StreetAddress` コレクションと共に使用します。

[!code-csharp[Distributor](../../../../samples/core/Cosmos/ModelBuilding/Distributor.cs?name=Distributor)]

所有されているエンティティは、明示的なキー値を格納する必要はありません。

[!code-csharp[OwnedCollection](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=OwnedCollection)]

これは、次のように永続化されます。

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

EF Core には、追跡対象のすべてのエンティティに対して、内部で常に一意のキー値が必要です。 所有されている型のコレクションに対して既定で作成される主キーは、所有者を指す外部キー プロパティと、JSON 配列内のインデックスに対応する `int` プロパティで構成されます。 これらの値を取得するには、エントリ API を使用します。

[!code-csharp[ImpliedProperties](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=ImpliedProperties)]

> [!TIP]
> 必要に応じて、所有されているエンティティ型の既定の主キーは変更できますが、その場合、キー値は明示的に指定する必要があります。

## <a name="working-with-disconnected-entities"></a>接続解除エンティティの使用

すべてのアイテムには、特定のパーティション キーに対して一意な `id` 値が必要です。 既定では、EF Core は、' | ' を区切り記号として使用して、識別子と主キーの値を連結して値を生成します。 このキー値は、エンティティが `Added` 状態になったときにのみ生成されます。 これは、.NET 型にその値を保存する `id` プロパティがない場合に[エンティティをアタッチするとき](../../saving/disconnected-entities.md)に問題になる場合があります。

この制限を回避するには、`id` 値を手動で作成して設定するか、エンティティをまず追加済みとしてマークして、その後目的の状態に変更します。

[!code-csharp[Attach](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?highlight=4&name=Attach)]

結果の JSON は次のようになります。

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
