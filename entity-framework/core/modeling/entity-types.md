---
title: エンティティ型-EF Core
description: Entity Framework Core を使用してエンティティ型を構成およびマップする方法
author: roji
ms.date: 10/06/2020
uid: core/modeling/entity-types
ms.openlocfilehash: 9094193640e7cab6db3fed7ae0ab818a455156ca
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429586"
---
# <a name="entity-types"></a><span data-ttu-id="8ad1a-103">エンティティ型</span><span class="sxs-lookup"><span data-stu-id="8ad1a-103">Entity Types</span></span>

<span data-ttu-id="8ad1a-104">型の DbSet をコンテキストに含めることは、それが EF Core のモデルに含まれることを意味します。通常、このような型は *エンティティ* として参照されます。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-104">Including a DbSet of a type on your context means that it is included in EF Core's model; we usually refer to such a type as an *entity*.</span></span> <span data-ttu-id="8ad1a-105">EF Core は、データベースとの間でエンティティインスタンスの読み取りと書き込みを行うことができます。リレーショナルデータベースを使用している場合は、EF Core によって、移行によってエンティティのテーブルを作成できます。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-105">EF Core can read and write entity instances from/to the database, and if you're using a relational database, EF Core can create tables for your entities via migrations.</span></span>

## <a name="including-types-in-the-model"></a><span data-ttu-id="8ad1a-106">モデルに型を含める</span><span class="sxs-lookup"><span data-stu-id="8ad1a-106">Including types in the model</span></span>

<span data-ttu-id="8ad1a-107">慣例により、コンテキストの DbSet プロパティで公開される型は、エンティティとしてモデルに含まれます。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-107">By convention, types that are exposed in DbSet properties on your context are included in the model as entities.</span></span> <span data-ttu-id="8ad1a-108">メソッドに指定されているエンティティ型は `OnModelCreating` 、他の検出されたエンティティ型のナビゲーションプロパティを再帰的に調べることによって検出される型と同様にも含まれます。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-108">Entity types that are specified in the `OnModelCreating` method are also included, as are any types that are found by recursively exploring the navigation properties of other discovered entity types.</span></span>

<span data-ttu-id="8ad1a-109">以下のコードサンプルでは、すべての型が含まれています。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-109">In the code sample below, all types are included:</span></span>

* <span data-ttu-id="8ad1a-110">`Blog` は、コンテキストの DbSet プロパティで公開されているため、含まれています。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-110">`Blog` is included because it's exposed in a DbSet property on the context.</span></span>
* <span data-ttu-id="8ad1a-111">`Post` は、ナビゲーションプロパティを使用して検出されるため、含まれてい `Blog.Posts` ます。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-111">`Post` is included because it's discovered via the `Blog.Posts` navigation property.</span></span>
* <span data-ttu-id="8ad1a-112">`AuditEntry` はで指定されているため `OnModelCreating` です。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-112">`AuditEntry` because it is specified in `OnModelCreating`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a><span data-ttu-id="8ad1a-113">モデルから型を除外する</span><span class="sxs-lookup"><span data-stu-id="8ad1a-113">Excluding types from the model</span></span>

<span data-ttu-id="8ad1a-114">モデルに型を含めない場合は、その型を除外することができます。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-114">If you don't want a type to be included in the model, you can exclude it:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="8ad1a-115">データの注釈</span><span class="sxs-lookup"><span data-stu-id="8ad1a-115">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="8ad1a-116">Fluent API</span><span class="sxs-lookup"><span data-stu-id="8ad1a-116">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

### <a name="excluding-from-migrations"></a><span data-ttu-id="8ad1a-117">移行から除外する</span><span class="sxs-lookup"><span data-stu-id="8ad1a-117">Excluding from migrations</span></span>

> [!NOTE]
> <span data-ttu-id="8ad1a-118">EF Core 5.0 では、移行からテーブルを除外する機能が追加されました。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-118">The ability to exclude tables from migrations was added in EF Core 5.0.</span></span>

<span data-ttu-id="8ad1a-119">同じエンティティ型を複数の型にマップすると便利な場合があり `DbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-119">It is sometimes useful to have the same entity type mapped in multiple `DbContext` types.</span></span> <span data-ttu-id="8ad1a-120">これは、境界付けられたコンテキストごとに異なる型を使用することが一般的である、境界付けられた [コンテキスト](https://www.martinfowler.com/bliki/BoundedContext.html)を使用する場合に特に当てはまり `DbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-120">This is especially true when using [bounded contexts](https://www.martinfowler.com/bliki/BoundedContext.html), for which it is common to have a different `DbContext` type for each bounded context.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableExcludeFromMigrations.cs?name=TableExcludeFromMigrations&highlight=4)]

<span data-ttu-id="8ad1a-121">この構成を使用すると、テーブルは作成されませんが、 `AspNetUsers` `IdentityUser` モデルに含まれ、通常どおりに使用できます。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-121">With this configuration migrations will not create the `AspNetUsers` table, but `IdentityUser` is still included in the model and can be used normally.</span></span>

<span data-ttu-id="8ad1a-122">移行を使用してテーブルの管理を再開する必要がある場合は、が除外されないように新しい移行を作成する必要があり `AspNetUsers` ます。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-122">If you need to start managing the table using migrations again then a new migration should be created where `AspNetUsers` is not excluded.</span></span> <span data-ttu-id="8ad1a-123">次の移行には、テーブルに加えられた変更がすべて含まれるようになります。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-123">The next migration will now contain any changes made to the table.</span></span>

## <a name="table-name"></a><span data-ttu-id="8ad1a-124">テーブル名</span><span class="sxs-lookup"><span data-stu-id="8ad1a-124">Table name</span></span>

<span data-ttu-id="8ad1a-125">慣例により、各エンティティ型は、エンティティを公開する DbSet プロパティと同じ名前のデータベーステーブルにマップされるように設定されます。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-125">By convention, each entity type will be set up to map to a database table with the same name as the DbSet property that exposes the entity.</span></span> <span data-ttu-id="8ad1a-126">指定されたエンティティに DbSet が存在しない場合は、クラス名が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-126">If no DbSet exists for the given entity, the class name is used.</span></span>

<span data-ttu-id="8ad1a-127">テーブル名は手動で構成できます。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-127">You can manually configure the table name:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="8ad1a-128">データの注釈</span><span class="sxs-lookup"><span data-stu-id="8ad1a-128">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="8ad1a-129">Fluent API</span><span class="sxs-lookup"><span data-stu-id="8ad1a-129">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

<span data-ttu-id="8ad1a-130">\*\*_</span><span class="sxs-lookup"><span data-stu-id="8ad1a-130">\*\*_</span></span>

## <a name="table-schema"></a><span data-ttu-id="8ad1a-131">テーブル スキーマ</span><span class="sxs-lookup"><span data-stu-id="8ad1a-131">Table schema</span></span>

<span data-ttu-id="8ad1a-132">リレーショナルデータベースを使用する場合、テーブルはデータベースの既定のスキーマで作成されます。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-132">When using a relational database, tables are by convention created in your database's default schema.</span></span> <span data-ttu-id="8ad1a-133">たとえば、Microsoft SQL Server はスキーマを使用し `dbo` ます (SQLite はスキーマをサポートしていません)。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-133">For example, Microsoft SQL Server will use the `dbo` schema (SQLite does not support schemas).</span></span>

<span data-ttu-id="8ad1a-134">次のように、特定のスキーマで作成されるテーブルを構成できます。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-134">You can configure tables to be created in a specific schema as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="8ad1a-135">データの注釈</span><span class="sxs-lookup"><span data-stu-id="8ad1a-135">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="8ad1a-136">Fluent API</span><span class="sxs-lookup"><span data-stu-id="8ad1a-136">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

<span data-ttu-id="8ad1a-137">_\*\*</span><span class="sxs-lookup"><span data-stu-id="8ad1a-137">_\*\*</span></span>

<span data-ttu-id="8ad1a-138">各テーブルのスキーマを指定するのではなく、fluent API を使用してモデルレベルで既定のスキーマを定義することもできます。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-138">Rather than specifying the schema for each table, you can also define the default schema at the model level with the fluent API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

<span data-ttu-id="8ad1a-139">既定のスキーマを設定すると、シーケンスなどの他のデータベースオブジェクトも影響を受けることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-139">Note that setting the default schema will also affect other database objects, such as sequences.</span></span>

## <a name="view-mapping"></a><span data-ttu-id="8ad1a-140">マッピングの表示</span><span class="sxs-lookup"><span data-stu-id="8ad1a-140">View mapping</span></span>

<span data-ttu-id="8ad1a-141">エンティティ型は、Fluent API を使用してデータベースビューにマップできます。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-141">Entity types can be mapped to database views using the Fluent API.</span></span>

> [!Note]
> <span data-ttu-id="8ad1a-142">EF では、参照されているビューが既にデータベースに存在することを前提としています。このビューは、移行時に自動的に作成されません。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-142">EF will assume that the referenced view already exists in the database, it will not create it automatically in a migration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ViewNameAndSchema.cs?name=ViewNameAndSchema&highlight=1)]

 <span data-ttu-id="8ad1a-143">ビューにマッピングすると、既定のテーブルマッピングが削除されますが、EF 5.0 以降では、エンティティ型を明示的にテーブルにマップすることもできます。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-143">Mapping to a view will remove the default table mapping, but starting with EF 5.0 the entity type can also be mapped to a table explicitly.</span></span> <span data-ttu-id="8ad1a-144">この場合、クエリにはクエリマッピングが使用され、更新にはテーブルマッピングが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-144">In this case the query mapping will be used for queries and the table mapping will be used for updates.</span></span>

> [!TIP]
> <span data-ttu-id="8ad1a-145">メモリ内のプロバイダーを使用してビューにマップされたエンティティ型をテストするには、を使用してクエリにマップし `ToInMemoryQuery` ます。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-145">To test entity types mapped to views using the in-memory provider map them to a query via `ToInMemoryQuery`.</span></span> <span data-ttu-id="8ad1a-146">詳細については、この手法を使用した実行可能な [サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8ad1a-146">See a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) using this technique for more details.</span></span>
