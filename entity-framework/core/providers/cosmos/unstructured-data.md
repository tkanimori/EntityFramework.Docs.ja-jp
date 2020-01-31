---
title: Azure Cosmos DB プロバイダー-非構造化データの操作-EF Core
description: Entity Framework Core を使用して Azure Cosmos DB 非構造化データを操作する方法
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/unstructured-data
ms.openlocfilehash: 69f979d46174ff56310b334f28438ac271f45155
ms.sourcegitcommit: b3cf5d2e3cb170b9916795d1d8c88678269639b1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2020
ms.locfileid: "76888097"
---
# <a name="working-with-unstructured-data-in-ef-core-azure-cosmos-db-provider"></a>EF Core Azure Cosmos DB プロバイダーでの非構造化データの操作

EF Core は、モデルで定義されたスキーマに従ったデータを簡単に操作できるように設計されています。 ただし、Azure Cosmos DB の長所の1つは、格納されるデータの構造に柔軟性があることです。

## <a name="accessing-the-raw-json"></a>生の JSON へのアクセス

EF Core によって追跡されないプロパティには、ストアから受信したデータと格納されるデータを表す `JObject` を含む `"__jObject"`[という名前の特殊](../../modeling/shadow-properties.md)なプロパティを使用してアクセスできます。

[!code-csharp[Unmapped](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=23,24&name=Unmapped)]

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
    "_rid": "eLMaAK8TzkIBAAAAAAAAAA==",
    "_self": "dbs/eLMaAA==/colls/eLMaAK8TzkI=/docs/eLMaAK8TzkIBAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683e-0a12bf8d01d5\"",
    "_attachments": "attachments/",
    "BillingAddress": "Clarence House",
    "_ts": 1568164374
}
```

> [!WARNING]
> `"__jObject"` プロパティは EF Core インフラストラクチャの一部であり、将来のリリースで動作が異なる可能性があるため、最後の手段としてのみ使用してください。

> [!NOTE]
> エンティティに対する変更は、`SaveChanges`中に `"__jObject"` に格納されている値よりも優先されます。

## <a name="using-cosmosclient"></a>CosmosClient の使用

EF Core から完全に切り離すには、 [AZURE COSMOS DB SDK の一部](/azure/cosmos-db/sql-api-get-started)である[CosmosClient](/dotnet/api/Microsoft.Azure.Cosmos.CosmosClient)オブジェクトを `DbContext`から取得します。

[!code-csharp[CosmosClient](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=3&name=CosmosClient)]

## <a name="missing-property-values"></a>プロパティ値がありません

前の例では、順序から `"TrackingNumber"` プロパティを削除しました。 Cosmos DB でのインデックス作成がどのように機能するのかによって、プロジェクション以外の場所に存在しないプロパティを参照するクエリは、予期しない結果を返す可能性があります。 例:

[!code-csharp[MissingProperties](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?name=MissingProperties)]

並べ替えられたクエリは実際には結果を返しません。 つまり、ストアを直接操作するときは、常に EF Core によってマップされたプロパティを設定する必要があります。

> [!NOTE]
> この動作は、Cosmos の将来のバージョンで変更される可能性があります。 たとえば、インデックス作成ポリシーで複合インデックス {Id/を定義している場合は、 ASC、TrackingNumber/? ASC)}、"ORDER BY c.Id ASC, c. 識別子 ASC ' を持つクエリでは、`"TrackingNumber"` プロパティがない項目__が返され__ます。
