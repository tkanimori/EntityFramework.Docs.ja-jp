---
title: Azure Cosmos DB プロバイダー-非構造化データの操作-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 09/12/2019
ms.assetid: b47d41b6-984f-419a-ab10-2ed3b95e3919
uid: core/providers/cosmos/unstructured-data
ms.openlocfilehash: 86bb0f7915c8a2561e7d5cd5dffc27474218a112
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71150771"
---
# <a name="working-with-unstructured-data-in-ef-core-azure-cosmos-db-provider"></a>EF Core Azure Cosmos DB プロバイダーでの非構造化データの操作

EF Core は、モデルで定義されたスキーマに従ったデータを簡単に操作できるように設計されています。 ただし、Azure Cosmos DB の長所の1つは、格納されるデータの構造に柔軟性があることです。

## <a name="accessing-the-raw-json"></a>生の JSON へのアクセス

EF Core によって追跡されない[プロパティには](../../modeling/shadow-properties.md)、ストアから受信したデータと格納される`"__jObject"`データを表す`JObject`を含む、という名前の特殊なプロパティを使用してアクセスできます。

[!code-csharp[Unmapped](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=21-23&name=Unmapped)]

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
    "_rid": "eLMaAK8TzkIBAAAAAAAAAA==",
    "_self": "dbs/eLMaAA==/colls/eLMaAK8TzkI=/docs/eLMaAK8TzkIBAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683e-0a12bf8d01d5\"",
    "_attachments": "attachments/",
    "BillingAddress": "Clarence House",
    "_ts": 1568164374
}
```

> [!WARNING]
> この`"__jObject"`プロパティは EF Core インフラストラクチャの一部であるため、最後の手段としてのみ使用してください。将来のリリースで動作が異なる可能性があるためです。

> [!NOTE]
> エンティティに対する変更は、の間`"__jObject"` `SaveChanges`に格納されている値よりも優先されます。

## <a name="using-cosmosclient"></a>CosmosClient の使用

EF Core から完全に切り離すには`CosmosClient` 、次の`DbContext`ように[Azure Cosmos DB SDK の一部](https://docs.microsoft.com/en-us/azure/cosmos-db/sql-api-get-started)であるオブジェクトを取得します。

[!code-csharp[CosmosClient](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=3&name=CosmosClient)]

## <a name="missing-property-values"></a>プロパティ値がありません

前の例では、順序`"TrackingNumber"`からプロパティを削除しました。 Cosmos DB でのインデックス作成がどのように機能するのかによって、プロジェクション以外の場所に存在しないプロパティを参照するクエリは、予期しない結果を返す可能性があります。 次に例を示します。

[!code-csharp[MissingProperties](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?name=MissingProperties)]

並べ替えられたクエリは実際には結果を返しません。 つまり、ストアを直接操作するときは、常に EF Core によってマップされたプロパティを設定する必要があります。

> [!NOTE]
> この動作は、Cosmos の将来のバージョンで変更される可能性があります。 たとえば、インデックス作成ポリシーで複合インデックス {Id/を定義している場合は、 ASC、TrackingNumber/? ASC)}__が ' ORDER__ BY c.Id asc, c. 識別子 asc ' を持つクエリは、 `"TrackingNumber"`プロパティが不足している項目を返します。