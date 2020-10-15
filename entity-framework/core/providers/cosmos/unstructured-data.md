---
title: Azure Cosmos DB プロバイダー-非構造化データの操作-EF Core
description: Entity Framework Core を使用して Azure Cosmos DB 非構造化データを操作する方法
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/unstructured-data
ms.openlocfilehash: ac497c9f5540557b931db935f4f3ca480edf010d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064025"
---
# <a name="working-with-unstructured-data-in-ef-core-azure-cosmos-db-provider"></a><span data-ttu-id="fe44f-103">EF Core Azure Cosmos DB プロバイダーでの非構造化データの操作</span><span class="sxs-lookup"><span data-stu-id="fe44f-103">Working with Unstructured Data in EF Core Azure Cosmos DB Provider</span></span>

<span data-ttu-id="fe44f-104">EF Core は、モデルで定義されたスキーマに従ったデータを簡単に操作できるように設計されています。</span><span class="sxs-lookup"><span data-stu-id="fe44f-104">EF Core was designed to make it easy to work with data that follows a schema defined in the model.</span></span> <span data-ttu-id="fe44f-105">ただし、Azure Cosmos DB の長所の1つは、格納されるデータの構造に柔軟性があることです。</span><span class="sxs-lookup"><span data-stu-id="fe44f-105">However one of the strengths of Azure Cosmos DB is the flexibility in the shape of the data stored.</span></span>

## <a name="accessing-the-raw-json"></a><span data-ttu-id="fe44f-106">生の JSON へのアクセス</span><span class="sxs-lookup"><span data-stu-id="fe44f-106">Accessing the raw JSON</span></span>

<span data-ttu-id="fe44f-107">EF Core によって追跡されない [プロパティには](xref:core/modeling/shadow-properties) 、 `"__jObject"` `JObject` ストアから受信したデータと格納されるデータを表すを含む、という名前の特殊なプロパティを使用してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="fe44f-107">It is possible to access the properties that are not tracked by EF Core through a special property in [shadow-state](xref:core/modeling/shadow-properties) named `"__jObject"` that contains a `JObject` representing the data recieved from the store and data that will be stored:</span></span>

[!code-csharp[Unmapped](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=23,24&name=Unmapped)]

```json
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
> <span data-ttu-id="fe44f-108">`"__jObject"`このプロパティは EF Core インフラストラクチャの一部であるため、最後の手段としてのみ使用してください。将来のリリースで動作が異なる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="fe44f-108">The `"__jObject"` property is part of the EF Core infrastructure and should only be used as a last resort as it is likely to have different behavior in future releases.</span></span>

> [!NOTE]
> <span data-ttu-id="fe44f-109">エンティティに対する変更は、の間に格納されている値よりも優先され `"__jObject"` `SaveChanges` ます。</span><span class="sxs-lookup"><span data-stu-id="fe44f-109">Changes to the entity will override the values stored in `"__jObject"` during `SaveChanges`.</span></span>

## <a name="using-cosmosclient"></a><span data-ttu-id="fe44f-110">CosmosClient の使用</span><span class="sxs-lookup"><span data-stu-id="fe44f-110">Using CosmosClient</span></span>

<span data-ttu-id="fe44f-111">EF Core から完全に切り離すには、次のように[AZURE COSMOS DB SDK の一部](/azure/cosmos-db/sql-api-get-started)である[CosmosClient](/dotnet/api/Microsoft.Azure.Cosmos.CosmosClient)オブジェクトを取得し `DbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="fe44f-111">To decouple completely from EF Core get the [CosmosClient](/dotnet/api/Microsoft.Azure.Cosmos.CosmosClient) object that is [part of the Azure Cosmos DB SDK](/azure/cosmos-db/sql-api-get-started) from `DbContext`:</span></span>

[!code-csharp[CosmosClient](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=3&name=CosmosClient)]

## <a name="missing-property-values"></a><span data-ttu-id="fe44f-112">プロパティ値がありません</span><span class="sxs-lookup"><span data-stu-id="fe44f-112">Missing property values</span></span>

<span data-ttu-id="fe44f-113">前の例では、順序からプロパティを削除しました `"TrackingNumber"` 。</span><span class="sxs-lookup"><span data-stu-id="fe44f-113">In the previous example we removed the `"TrackingNumber"` property from the order.</span></span> <span data-ttu-id="fe44f-114">Cosmos DB でのインデックス作成がどのように機能するのかによって、プロジェクション以外の場所に存在しないプロパティを参照するクエリは、予期しない結果を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="fe44f-114">Because of how indexing works in Cosmos DB, queries that reference the missing property somewhere else than in the projection could return unexpected results.</span></span> <span data-ttu-id="fe44f-115">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="fe44f-115">For example:</span></span>

[!code-csharp[MissingProperties](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?name=MissingProperties)]

<span data-ttu-id="fe44f-116">並べ替えられたクエリは実際には結果を返しません。</span><span class="sxs-lookup"><span data-stu-id="fe44f-116">The sorted query actually returns no results.</span></span> <span data-ttu-id="fe44f-117">つまり、ストアを直接操作するときは、常に EF Core によってマップされたプロパティを設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fe44f-117">This means that one should take care to always populate properties mapped by EF Core when working with the store directly.</span></span>

> [!NOTE]
> <span data-ttu-id="fe44f-118">この動作は、Cosmos の将来のバージョンで変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="fe44f-118">This behavior might change in future versions of Cosmos.</span></span> <span data-ttu-id="fe44f-119">たとえば、インデックス作成ポリシーで複合インデックス {Id/を定義している場合は、</span><span class="sxs-lookup"><span data-stu-id="fe44f-119">For instance, currently if the indexing policy defines the composite index {Id/?</span></span> <span data-ttu-id="fe44f-120">ASC、TrackingNumber/?</span><span class="sxs-lookup"><span data-stu-id="fe44f-120">ASC, TrackingNumber/?</span></span> <span data-ttu-id="fe44f-121">ASC)}、' ORDER BY c.Id ASC, c. 識別子 ASC ' を持つ __クエリは、__ プロパティが不足している項目を返し `"TrackingNumber"` ます。</span><span class="sxs-lookup"><span data-stu-id="fe44f-121">ASC)}, then a query that has 'ORDER BY c.Id ASC, c.Discriminator ASC' __would__ return items that are missing the `"TrackingNumber"` property.</span></span>
