---
title: Azure Cosmos DB プロバイダー - EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 09/12/2019
ms.assetid: 28264681-4486-4891-888c-be5e4ade24f1
uid: core/providers/cosmos/index
ms.openlocfilehash: 683436aa485d2fef9aa8bf6c6ff02b00dfeb28cf
ms.sourcegitcommit: 2caec1e63f2ce1d9439ef6193df5a77da2fedd0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71317560"
---
# <a name="ef-core-azure-cosmos-db-provider"></a>EF Core Azure Cosmos DB プロバイダー

>[!NOTE]
> このプロバイダーは、EF Core 3.0 で新しく追加されたものです。

このデータベース プロバイダーにより、Azure Cosmos DB と共に Entity Framework Core を使用できます。 このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。

このセクションを読む前に、[Azure Cosmos DB のドキュメント](https://docs.microsoft.com/en-us/azure/cosmos-db/introduction)を理解することを強くお勧めします。

## <a name="install"></a>インストール

[Microsoft.EntityFrameworkCore.Cosmos NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/)をインストールします。

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Cosmos
```

## <a name="get-started"></a>開始するには

> [!TIP]  
> この記事の[サンプルは GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Cosmos) で確認できます。

他のプロバイダーと同様に、最初の手順は `UseCosmos` を呼び出すことです。

[!code-csharp[Configuration](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Configuration)]

> [!WARNING]
> ここでは、わかりやすくするためにエンドポイントとキーをハードコーディングしていますが、運用アプリでは、これらは[安全に保存](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager)する必要があります。

この例では、`Order` は、[所有されている型](../../modeling/owned-entities.md) `StreetAddress`への参照を持つ単純なエンティティです。

[!code-csharp[Order](../../../../samples/core/Cosmos/ModelBuilding/Order.cs?name=Order)]

[!code-csharp[StreetAddress](../../../../samples/core/Cosmos/ModelBuilding/StreetAddress.cs?name=StreetAddress)]

データの保存とクエリは、通常の EF のパターンに従います。

[!code-csharp[HelloCosmos](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=HelloCosmos)]

> [!IMPORTANT]
> 必要なコレクションの作成と、モデルに存在する場合の[シード データ](../../modeling/data-seeding.md)の挿入には、`EnsureCreated` を呼び出す必要があります。 ただし、`EnsureCreated` は、パフォーマンスの問題を引き起こす可能性があるため、通常の操作ではなく、配置時にのみ呼び出す必要があります。

## <a name="cosmos-specific-model-customization"></a>Cosmos 固有のモデルのカスタマイズ

既定では、すべてのエンティティ型は、(この場合は `"OrderContext"` の) 派生コンテキストに基づいて命名された同じコンテナーにマップされます。 既定のコンテナー名を変更するには、次のように `HasDefaultContainer` を使用します。

[!code-csharp[DefaultContainer](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=DefaultContainer)]

エンティティ型を別のコンテナーにマップするには、次のように `ToContainer` を使用します。

[!code-csharp[Container](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Container)]

EF Core では、派生エンティティ型がない場合でも、特定の項目が表すエンティティ型の識別に識別子の値が追加されます。 識別子の名前と値は[変更できます](../../modeling/inheritance.md)。

## <a name="embedded-entities"></a>埋め込みエンティティ

Cosmos 所有エンティティは、所有者と同じアイテムに埋め込まれます。 プロパティ名を変更するには、次のように `ToJsonProperty` を使用します。

[!code-csharp[PropertyNames](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PropertyNames)]

この構成では、上記の例の順序は次のように格納されます。

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
