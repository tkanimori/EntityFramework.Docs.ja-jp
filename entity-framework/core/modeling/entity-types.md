---
title: エンティティ型-EF Core
description: Entity Framework Core を使用してエンティティ型を構成およびマップする方法
author: roji
ms.date: 12/03/2019
uid: core/modeling/entity-types
ms.openlocfilehash: fead7f9e37efb7f674f429acbfd16c2ca78480d4
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071512"
---
# <a name="entity-types"></a><span data-ttu-id="072f1-103">エンティティ型</span><span class="sxs-lookup"><span data-stu-id="072f1-103">Entity Types</span></span>

<span data-ttu-id="072f1-104">型の DbSet をコンテキストに含めることは、それが EF Core のモデルに含まれることを意味します。通常、このような型は *エンティティ*として参照されます。</span><span class="sxs-lookup"><span data-stu-id="072f1-104">Including a DbSet of a type on your context means that it is included in EF Core's model; we usually refer to such a type as an *entity*.</span></span> <span data-ttu-id="072f1-105">EF Core は、データベースとの間でエンティティインスタンスの読み取りと書き込みを行うことができます。リレーショナルデータベースを使用している場合は、EF Core によって、移行によってエンティティのテーブルを作成できます。</span><span class="sxs-lookup"><span data-stu-id="072f1-105">EF Core can read and write entity instances from/to the database, and if you're using a relational database, EF Core can create tables for your entities via migrations.</span></span>

## <a name="including-types-in-the-model"></a><span data-ttu-id="072f1-106">モデルに型を含める</span><span class="sxs-lookup"><span data-stu-id="072f1-106">Including types in the model</span></span>

<span data-ttu-id="072f1-107">慣例により、コンテキストの DbSet プロパティで公開される型は、エンティティとしてモデルに含まれます。</span><span class="sxs-lookup"><span data-stu-id="072f1-107">By convention, types that are exposed in DbSet properties on your context are included in the model as entities.</span></span> <span data-ttu-id="072f1-108">メソッドに指定されているエンティティ型は `OnModelCreating` 、他の検出されたエンティティ型のナビゲーションプロパティを再帰的に調べることによって検出される型と同様にも含まれます。</span><span class="sxs-lookup"><span data-stu-id="072f1-108">Entity types that are specified in the `OnModelCreating` method are also included, as are any types that are found by recursively exploring the navigation properties of other discovered entity types.</span></span>

<span data-ttu-id="072f1-109">以下のコードサンプルでは、すべての型が含まれています。</span><span class="sxs-lookup"><span data-stu-id="072f1-109">In the code sample below, all types are included:</span></span>

* <span data-ttu-id="072f1-110">`Blog` は、コンテキストの DbSet プロパティで公開されているため、含まれています。</span><span class="sxs-lookup"><span data-stu-id="072f1-110">`Blog` is included because it's exposed in a DbSet property on the context.</span></span>
* <span data-ttu-id="072f1-111">`Post` は、ナビゲーションプロパティを使用して検出されるため、含まれてい `Blog.Posts` ます。</span><span class="sxs-lookup"><span data-stu-id="072f1-111">`Post` is included because it's discovered via the `Blog.Posts` navigation property.</span></span>
* <span data-ttu-id="072f1-112">`AuditEntry` はで指定されているため `OnModelCreating` です。</span><span class="sxs-lookup"><span data-stu-id="072f1-112">`AuditEntry` because it is specified in `OnModelCreating`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a><span data-ttu-id="072f1-113">モデルから型を除外する</span><span class="sxs-lookup"><span data-stu-id="072f1-113">Excluding types from the model</span></span>

<span data-ttu-id="072f1-114">モデルに型を含めない場合は、その型を除外することができます。</span><span class="sxs-lookup"><span data-stu-id="072f1-114">If you don't want a type to be included in the model, you can exclude it:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="072f1-115">データの注釈</span><span class="sxs-lookup"><span data-stu-id="072f1-115">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="072f1-116">Fluent API</span><span class="sxs-lookup"><span data-stu-id="072f1-116">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

## <a name="table-name"></a><span data-ttu-id="072f1-117">テーブル名</span><span class="sxs-lookup"><span data-stu-id="072f1-117">Table name</span></span>

<span data-ttu-id="072f1-118">慣例により、各エンティティ型は、エンティティを公開する DbSet プロパティと同じ名前のデータベーステーブルにマップされるように設定されます。</span><span class="sxs-lookup"><span data-stu-id="072f1-118">By convention, each entity type will be set up to map to a database table with the same name as the DbSet property that exposes the entity.</span></span> <span data-ttu-id="072f1-119">指定されたエンティティに DbSet が存在しない場合は、クラス名が使用されます。</span><span class="sxs-lookup"><span data-stu-id="072f1-119">If no DbSet exists for the given entity, the class name is used.</span></span>

<span data-ttu-id="072f1-120">テーブル名は手動で構成できます。</span><span class="sxs-lookup"><span data-stu-id="072f1-120">You can manually configure the table name:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="072f1-121">データの注釈</span><span class="sxs-lookup"><span data-stu-id="072f1-121">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="072f1-122">Fluent API</span><span class="sxs-lookup"><span data-stu-id="072f1-122">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

***

## <a name="table-schema"></a><span data-ttu-id="072f1-123">テーブル スキーマ</span><span class="sxs-lookup"><span data-stu-id="072f1-123">Table schema</span></span>

<span data-ttu-id="072f1-124">リレーショナルデータベースを使用する場合、テーブルはデータベースの既定のスキーマで作成されます。</span><span class="sxs-lookup"><span data-stu-id="072f1-124">When using a relational database, tables are by convention created in your database's default schema.</span></span> <span data-ttu-id="072f1-125">たとえば、Microsoft SQL Server はスキーマを使用し `dbo` ます (SQLite はスキーマをサポートしていません)。</span><span class="sxs-lookup"><span data-stu-id="072f1-125">For example, Microsoft SQL Server will use the `dbo` schema (SQLite does not support schemas).</span></span>

<span data-ttu-id="072f1-126">次のように、特定のスキーマで作成されるテーブルを構成できます。</span><span class="sxs-lookup"><span data-stu-id="072f1-126">You can configure tables to be created in a specific schema as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="072f1-127">データの注釈</span><span class="sxs-lookup"><span data-stu-id="072f1-127">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="072f1-128">Fluent API</span><span class="sxs-lookup"><span data-stu-id="072f1-128">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

***

<span data-ttu-id="072f1-129">各テーブルのスキーマを指定するのではなく、fluent API を使用してモデルレベルで既定のスキーマを定義することもできます。</span><span class="sxs-lookup"><span data-stu-id="072f1-129">Rather than specifying the schema for each table, you can also define the default schema at the model level with the fluent API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

<span data-ttu-id="072f1-130">既定のスキーマを設定すると、シーケンスなどの他のデータベースオブジェクトも影響を受けることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="072f1-130">Note that setting the default schema will also affect other database objects, such as sequences.</span></span>
