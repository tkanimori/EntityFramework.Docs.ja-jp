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
# <a name="working-with-unstructured-data-in-ef-core-azure-cosmos-db-provider"></a><span data-ttu-id="2eb08-102">EF Core Azure Cosmos DB プロバイダーでの非構造化データの操作</span><span class="sxs-lookup"><span data-stu-id="2eb08-102">Working with Unstructured Data in EF Core Azure Cosmos DB Provider</span></span>

<span data-ttu-id="2eb08-103">EF Core は、モデルで定義されたスキーマに従ったデータを簡単に操作できるように設計されています。</span><span class="sxs-lookup"><span data-stu-id="2eb08-103">EF Core was designed to make it easy to work with data that follows a schema defined in the model.</span></span> <span data-ttu-id="2eb08-104">ただし、Azure Cosmos DB の長所の1つは、格納されるデータの構造に柔軟性があることです。</span><span class="sxs-lookup"><span data-stu-id="2eb08-104">However one of the strengths of Azure Cosmos DB is the flexibility in the shape of the data stored.</span></span>

## <a name="accessing-the-raw-json"></a><span data-ttu-id="2eb08-105">生の JSON へのアクセス</span><span class="sxs-lookup"><span data-stu-id="2eb08-105">Accessing the raw JSON</span></span>

<span data-ttu-id="2eb08-106">EF Core によって追跡されない[プロパティには](../../modeling/shadow-properties.md)、ストアから受信したデータと格納される`"__jObject"`データを表す`JObject`を含む、という名前の特殊なプロパティを使用してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="2eb08-106">It is possible to access the properties that are not tracked by EF Core through a special property in [shadow-state](../../modeling/shadow-properties.md) named `"__jObject"` that contains a `JObject` representing the data recieved from the store and data that will be stored:</span></span>

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
> <span data-ttu-id="2eb08-107">この`"__jObject"`プロパティは EF Core インフラストラクチャの一部であるため、最後の手段としてのみ使用してください。将来のリリースで動作が異なる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="2eb08-107">The `"__jObject"` property is part of the EF Core infrastructure and should only be used as a last resort as it is likely to have different behavior in future releases.</span></span>

> [!NOTE]
> <span data-ttu-id="2eb08-108">エンティティに対する変更は、の間`"__jObject"` `SaveChanges`に格納されている値よりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="2eb08-108">Changes to the entity will override the values stored in `"__jObject"` during `SaveChanges`.</span></span>

## <a name="using-cosmosclient"></a><span data-ttu-id="2eb08-109">CosmosClient の使用</span><span class="sxs-lookup"><span data-stu-id="2eb08-109">Using CosmosClient</span></span>

<span data-ttu-id="2eb08-110">EF Core から完全に切り離すには`CosmosClient` 、次の`DbContext`ように[Azure Cosmos DB SDK の一部](https://docs.microsoft.com/en-us/azure/cosmos-db/sql-api-get-started)であるオブジェクトを取得します。</span><span class="sxs-lookup"><span data-stu-id="2eb08-110">To decouple completely from EF Core get the `CosmosClient` object that is [part of the Azure Cosmos DB SDK](https://docs.microsoft.com/en-us/azure/cosmos-db/sql-api-get-started) from `DbContext`:</span></span>

[!code-csharp[CosmosClient](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=3&name=CosmosClient)]

## <a name="missing-property-values"></a><span data-ttu-id="2eb08-111">プロパティ値がありません</span><span class="sxs-lookup"><span data-stu-id="2eb08-111">Missing property values</span></span>

<span data-ttu-id="2eb08-112">前の例では、順序`"TrackingNumber"`からプロパティを削除しました。</span><span class="sxs-lookup"><span data-stu-id="2eb08-112">In the previous example we removed the `"TrackingNumber"` property from the order.</span></span> <span data-ttu-id="2eb08-113">Cosmos DB でのインデックス作成がどのように機能するのかによって、プロジェクション以外の場所に存在しないプロパティを参照するクエリは、予期しない結果を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2eb08-113">Because of how indexing works in Cosmos DB, queries that reference the missing property somewhere else than in the projection could return unexpected results.</span></span> <span data-ttu-id="2eb08-114">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="2eb08-114">For example:</span></span>

[!code-csharp[MissingProperties](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?name=MissingProperties)]

<span data-ttu-id="2eb08-115">並べ替えられたクエリは実際には結果を返しません。</span><span class="sxs-lookup"><span data-stu-id="2eb08-115">The sorted query actually returns no results.</span></span> <span data-ttu-id="2eb08-116">つまり、ストアを直接操作するときは、常に EF Core によってマップされたプロパティを設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2eb08-116">This means that one should take care to always populate properties mapped by EF Core when working with the store directly.</span></span>

> [!NOTE]
> <span data-ttu-id="2eb08-117">この動作は、Cosmos の将来のバージョンで変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2eb08-117">This behavior might change in future versions of Cosmos.</span></span> <span data-ttu-id="2eb08-118">たとえば、インデックス作成ポリシーで複合インデックス {Id/を定義している場合は、</span><span class="sxs-lookup"><span data-stu-id="2eb08-118">For instance, currently if the indexing policy defines the composite index {Id/?</span></span> <span data-ttu-id="2eb08-119">ASC、TrackingNumber/?</span><span class="sxs-lookup"><span data-stu-id="2eb08-119">ASC, TrackingNumber/?</span></span> <span data-ttu-id="2eb08-120">ASC)}__が ' ORDER__ BY c.Id asc, c. 識別子 asc ' を持つクエリは、 `"TrackingNumber"`プロパティが不足している項目を返します。</span><span class="sxs-lookup"><span data-stu-id="2eb08-120">ASC)}, then a query the has 'ORDER BY c.Id ASC, c.Discriminator ASC' __would__ return items that are missing the `"TrackingNumber"` property.</span></span>