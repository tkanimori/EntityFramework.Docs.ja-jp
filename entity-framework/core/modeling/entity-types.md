---
title: エンティティ型-EF Core
description: Entity Framework Core を使用してエンティティ型を構成およびマップする方法
author: roji
ms.date: 12/03/2019
ms.assetid: cbe6935e-2679-4b77-8914-a8d772240cf1
uid: core/modeling/entity-types
ms.openlocfilehash: b3d9ad753637d021d9aa52965da38091ae690f77
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502415"
---
# <a name="entity-types"></a><span data-ttu-id="b02cc-103">エンティティ型</span><span class="sxs-lookup"><span data-stu-id="b02cc-103">Entity Types</span></span>

<span data-ttu-id="b02cc-104">型の DbSet をコンテキストに含めることは、それが EF Core のモデルに含まれることを意味します。通常、このような型は*エンティティ*として参照されます。</span><span class="sxs-lookup"><span data-stu-id="b02cc-104">Including a DbSet of a type on your context means that it is included in EF Core's model; we usually refer to such a type as an *entity*.</span></span> <span data-ttu-id="b02cc-105">EF Core は、データベースとの間でエンティティインスタンスの読み取りと書き込みを行うことができます。リレーショナルデータベースを使用している場合は、EF Core によって、移行によってエンティティのテーブルを作成できます。</span><span class="sxs-lookup"><span data-stu-id="b02cc-105">EF Core can read and write entity instances from/to the database, and if you're using a relational database, EF Core can create tables for your entities via migrations.</span></span>

## <a name="including-types-in-the-model"></a><span data-ttu-id="b02cc-106">モデルに型を含める</span><span class="sxs-lookup"><span data-stu-id="b02cc-106">Including types in the model</span></span>

<span data-ttu-id="b02cc-107">慣例により、コンテキストの DbSet プロパティで公開される型は、エンティティとしてモデルに含まれます。</span><span class="sxs-lookup"><span data-stu-id="b02cc-107">By convention, types that are exposed in DbSet properties on your context are included in the model as entities.</span></span> <span data-ttu-id="b02cc-108">`OnModelCreating` メソッドに指定されているエンティティ型は、他の検出されたエンティティ型のナビゲーションプロパティを再帰的に調べることによって検出される型と同様にも含まれます。</span><span class="sxs-lookup"><span data-stu-id="b02cc-108">Entity types that are specified in the `OnModelCreating` method are also included, as are any types that are found by recursively exploring the navigation properties of other discovered entity types.</span></span>

<span data-ttu-id="b02cc-109">以下のコードサンプルでは、すべての型が含まれています。</span><span class="sxs-lookup"><span data-stu-id="b02cc-109">In the code sample below, all types are included:</span></span>

* <span data-ttu-id="b02cc-110">`Blog` は、コンテキストの DbSet プロパティで公開されているため、含まれています。</span><span class="sxs-lookup"><span data-stu-id="b02cc-110">`Blog` is included because it's exposed in a DbSet property on the context.</span></span>
* <span data-ttu-id="b02cc-111">`Post` は、`Blog.Posts` ナビゲーションプロパティを使用して検出されるため、含まれています。</span><span class="sxs-lookup"><span data-stu-id="b02cc-111">`Post` is included because it's discovered via the `Blog.Posts` navigation property.</span></span>
* <span data-ttu-id="b02cc-112">`OnModelCreating`に指定されているため、`AuditEntry` します。</span><span class="sxs-lookup"><span data-stu-id="b02cc-112">`AuditEntry` because it is specified in `OnModelCreating`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a><span data-ttu-id="b02cc-113">モデルから型を除外する</span><span class="sxs-lookup"><span data-stu-id="b02cc-113">Excluding types from the model</span></span>

<span data-ttu-id="b02cc-114">モデルに型を含めない場合は、その型を除外することができます。</span><span class="sxs-lookup"><span data-stu-id="b02cc-114">If you don't want a type to be included in the model, you can exclude it:</span></span>

### <a name="data-annotationstabdata-annotations"></a>[<span data-ttu-id="b02cc-115">データの注釈</span><span class="sxs-lookup"><span data-stu-id="b02cc-115">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-apitabfluent-api"></a>[<span data-ttu-id="b02cc-116">Fluent API</span><span class="sxs-lookup"><span data-stu-id="b02cc-116">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

## <a name="table-name"></a><span data-ttu-id="b02cc-117">テーブル名</span><span class="sxs-lookup"><span data-stu-id="b02cc-117">Table name</span></span>

<span data-ttu-id="b02cc-118">慣例により、各エンティティ型は、エンティティを公開する DbSet プロパティと同じ名前のデータベーステーブルにマップされるように設定されます。</span><span class="sxs-lookup"><span data-stu-id="b02cc-118">By convention, each entity type will be set up to map to a database table with the same name as the DbSet property that exposes the entity.</span></span> <span data-ttu-id="b02cc-119">指定されたエンティティに DbSet が存在しない場合は、クラス名が使用されます。</span><span class="sxs-lookup"><span data-stu-id="b02cc-119">If no DbSet exists for the given entity, the class name is used.</span></span>

<span data-ttu-id="b02cc-120">テーブル名は手動で構成できます。</span><span class="sxs-lookup"><span data-stu-id="b02cc-120">You can manually configure the table name:</span></span>

### <a name="data-annotationstabdata-annotations"></a>[<span data-ttu-id="b02cc-121">データの注釈</span><span class="sxs-lookup"><span data-stu-id="b02cc-121">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-apitabfluent-api"></a>[<span data-ttu-id="b02cc-122">Fluent API</span><span class="sxs-lookup"><span data-stu-id="b02cc-122">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

***

## <a name="table-schema"></a><span data-ttu-id="b02cc-123">テーブル スキーマ</span><span class="sxs-lookup"><span data-stu-id="b02cc-123">Table schema</span></span>

<span data-ttu-id="b02cc-124">リレーショナルデータベースを使用する場合、テーブルはデータベースの既定のスキーマで作成されます。</span><span class="sxs-lookup"><span data-stu-id="b02cc-124">When using a relational database, tables are by convention created in your database's default schema.</span></span> <span data-ttu-id="b02cc-125">たとえば、Microsoft SQL Server は `dbo` スキーマを使用します (SQLite はスキーマをサポートしていません)。</span><span class="sxs-lookup"><span data-stu-id="b02cc-125">For example, Microsoft SQL Server will use the `dbo` schema (SQLite does not support schemas).</span></span>

<span data-ttu-id="b02cc-126">次のように、特定のスキーマで作成されるテーブルを構成できます。</span><span class="sxs-lookup"><span data-stu-id="b02cc-126">You can configure tables to be created in a specific schema as follows:</span></span>

### <a name="data-annotationstabdata-annotations"></a>[<span data-ttu-id="b02cc-127">データの注釈</span><span class="sxs-lookup"><span data-stu-id="b02cc-127">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-apitabfluent-api"></a>[<span data-ttu-id="b02cc-128">Fluent API</span><span class="sxs-lookup"><span data-stu-id="b02cc-128">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

***

<span data-ttu-id="b02cc-129">各テーブルのスキーマを指定するのではなく、fluent API を使用してモデルレベルで既定のスキーマを定義することもできます。</span><span class="sxs-lookup"><span data-stu-id="b02cc-129">Rather than specifying the schema for each table, you can also define the default schema at the model level with the fluent API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

<span data-ttu-id="b02cc-130">既定のスキーマを設定すると、シーケンスなどの他のデータベースオブジェクトも影響を受けることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="b02cc-130">Note that setting the default schema will also affect other database objects, such as sequences.</span></span>
