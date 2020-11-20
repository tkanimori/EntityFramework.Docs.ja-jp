---
title: 継承-EF Core
description: Entity Framework Core を使用してエンティティ型の継承を構成する方法
author: AndriySvyryd
ms.date: 10/01/2020
uid: core/modeling/inheritance
ms.openlocfilehash: 33429bbc4a9941ff8ea98a8f99cc652c8ea26455
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003628"
---
# <a name="inheritance"></a><span data-ttu-id="c564e-103">継承</span><span class="sxs-lookup"><span data-stu-id="c564e-103">Inheritance</span></span>

<span data-ttu-id="c564e-104">EF では、.NET 型階層をデータベースにマップできます。</span><span class="sxs-lookup"><span data-stu-id="c564e-104">EF can map a .NET type hierarchy to a database.</span></span> <span data-ttu-id="c564e-105">これにより、基本型と派生型を使用して、通常どおりに .NET エンティティをコードに記述し、EF によって適切なデータベーススキーマの作成、クエリの実行などをシームレスに行うことができます。型階層のマッピング方法の実際の詳細は、プロバイダーに依存します。このページでは、リレーショナルデータベースのコンテキストでの継承のサポートについて説明します。</span><span class="sxs-lookup"><span data-stu-id="c564e-105">This allows you to write your .NET entities in code as usual, using base and derived types, and have EF seamlessly create the appropriate database schema, issue queries, etc. The actual details of how a type hierarchy is mapped are provider-dependent; this page describes inheritance support in the context of a relational database.</span></span>

## <a name="entity-type-hierarchy-mapping"></a><span data-ttu-id="c564e-106">エンティティ型階層のマッピング</span><span class="sxs-lookup"><span data-stu-id="c564e-106">Entity type hierarchy mapping</span></span>

<span data-ttu-id="c564e-107">慣例により、EF は基本型または派生型を自動的にスキャンしません。これは、階層内の CLR 型をマップする必要がある場合は、モデルでその型を明示的に指定する必要があることを意味します。</span><span class="sxs-lookup"><span data-stu-id="c564e-107">By convention, EF will not automatically scan for base or derived types; this means that if you want a CLR type in your hierarchy to be mapped, you must explicitly specify that type on your model.</span></span> <span data-ttu-id="c564e-108">たとえば、階層の基本データ型だけを指定しても、EF Core によってそのすべてのサブ型が暗黙的に含まれるとは限りません。</span><span class="sxs-lookup"><span data-stu-id="c564e-108">For example, specifying only the base type of a hierarchy will not cause EF Core to implicitly include all of its sub-types.</span></span>

<span data-ttu-id="c564e-109">次の例では、とそのサブクラスの DbSet を公開して `Blog` `RssBlog` います。</span><span class="sxs-lookup"><span data-stu-id="c564e-109">The following sample exposes a DbSet for `Blog` and its subclass `RssBlog`.</span></span> <span data-ttu-id="c564e-110">`Blog`に他のサブクラスがある場合は、モデルには含まれません。</span><span class="sxs-lookup"><span data-stu-id="c564e-110">If `Blog` has any other subclass, it will not be included in the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

> [!NOTE]
> <span data-ttu-id="c564e-111">TPH マッピングを使用する場合、データベース列は必要に応じて自動的に null 許容になります。</span><span class="sxs-lookup"><span data-stu-id="c564e-111">Database columns are automatically made nullable as necessary when using TPH mapping.</span></span> <span data-ttu-id="c564e-112">たとえば、列は `RssUrl` null 値を許容 `Blog` します。通常のインスタンスにはそのプロパティがないためです。</span><span class="sxs-lookup"><span data-stu-id="c564e-112">For example, the `RssUrl` column is nullable because regular `Blog` instances do not have that property.</span></span>

<span data-ttu-id="c564e-113">階層内の1つ以上のエンティティに対してを公開しない場合は、 `DbSet` FLUENT API を使用してモデルに含まれていることを確認することもできます。</span><span class="sxs-lookup"><span data-stu-id="c564e-113">If you don't want to expose a `DbSet` for one or more entities in the hierarchy, you can also use the Fluent API to ensure they are included in the model.</span></span>

> [!TIP]
> <span data-ttu-id="c564e-114">規則に依存しない場合は、を使用して基本型を明示的に指定でき `HasBaseType` ます。</span><span class="sxs-lookup"><span data-stu-id="c564e-114">If you don't rely on conventions, you can specify the base type explicitly using `HasBaseType`.</span></span> <span data-ttu-id="c564e-115">また、を使用し `.HasBaseType((Type)null)` て、階層からエンティティ型を削除することもできます。</span><span class="sxs-lookup"><span data-stu-id="c564e-115">You can also use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="table-per-hierarchy-and-discriminator-configuration"></a><span data-ttu-id="c564e-116">階層ごとのテーブルと識別子の構成</span><span class="sxs-lookup"><span data-stu-id="c564e-116">Table-per-hierarchy and discriminator configuration</span></span>

<span data-ttu-id="c564e-117">既定では、EF は、 *階層単位* (TPH) パターンを使用して継承をマップします。</span><span class="sxs-lookup"><span data-stu-id="c564e-117">By default, EF maps the inheritance using the *table-per-hierarchy* (TPH) pattern.</span></span> <span data-ttu-id="c564e-118">TPH は、1つのテーブルを使用して階層内のすべての型のデータを格納します。また、識別子列を使用して、各行が表す型を識別します。</span><span class="sxs-lookup"><span data-stu-id="c564e-118">TPH uses a single table to store the data for all types in the hierarchy, and a discriminator column is used to identify which type each row represents.</span></span>

<span data-ttu-id="c564e-119">上記のモデルは、次のデータベーススキーマにマップされます (各行に格納されているの型を識別する、暗黙的に作成された列に注意して `Discriminator` `Blog` ください)。</span><span class="sxs-lookup"><span data-stu-id="c564e-119">The model above is mapped to the following database schema (note the implicitly created `Discriminator` column, which identifies which type of `Blog` is stored in each row).</span></span>

![階層ごとのテーブルパターンを使用してブログエンティティ階層にクエリを実行した結果のスクリーンショット](_static/inheritance-tph-data.png)

<span data-ttu-id="c564e-121">識別子の列の名前と種類と、階層内の各型を識別するために使用される値を構成できます。</span><span class="sxs-lookup"><span data-stu-id="c564e-121">You can configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

<span data-ttu-id="c564e-122">上の例では、EF によって、階層の基本エンティティに対して暗黙的に [シャドウプロパティ](xref:core/modeling/shadow-properties) として識別子が追加されています。</span><span class="sxs-lookup"><span data-stu-id="c564e-122">In the examples above, EF added the discriminator implicitly as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="c564e-123">このプロパティは、次のように構成できます。</span><span class="sxs-lookup"><span data-stu-id="c564e-123">This property can be configured like any other:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

<span data-ttu-id="c564e-124">また、識別子は、エンティティ内の通常の .NET プロパティにもマップできます。</span><span class="sxs-lookup"><span data-stu-id="c564e-124">Finally, the discriminator can also be mapped to a regular .NET property in your entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

<span data-ttu-id="c564e-125">TPH パターンを使用する派生エンティティに対してクエリを実行すると、EF Core によってクエリの識別子列に述語が追加されます。</span><span class="sxs-lookup"><span data-stu-id="c564e-125">When querying for derived entities, which use the TPH pattern, EF Core adds a predicate over discriminator column in the query.</span></span> <span data-ttu-id="c564e-126">このフィルターにより、結果に含まれていない基本データ型または兄弟型に対して追加の行が取得されないようにします。</span><span class="sxs-lookup"><span data-stu-id="c564e-126">This filter makes sure that we don't get any additional rows for base types or sibling types not in the result.</span></span> <span data-ttu-id="c564e-127">基本エンティティに対してクエリを実行すると、階層内のすべてのエンティティの結果が取得されるため、基本エンティティ型に対してこのフィルター述語はスキップされます。</span><span class="sxs-lookup"><span data-stu-id="c564e-127">This filter predicate is skipped for the base entity type since querying for the base entity will get results for all the entities in the hierarchy.</span></span> <span data-ttu-id="c564e-128">クエリから結果を具体化する場合、モデル内のエンティティ型にマップされていない識別子の値を使用すると、結果を具体化する方法がわからないため、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="c564e-128">When materializing results from a query, if we come across a discriminator value, which isn't mapped to any entity type in the model, we throw an exception since we don't know how to materialize the results.</span></span> <span data-ttu-id="c564e-129">このエラーが発生するのは、EF モデルでマップされていない識別子の値を持つ行がデータベースに含まれている場合のみです。</span><span class="sxs-lookup"><span data-stu-id="c564e-129">This error only occurs if your database contains rows with discriminator values, which aren't mapped in the EF model.</span></span> <span data-ttu-id="c564e-130">このようなデータがある場合は、EF Core モデルの識別子マッピングを不完全としてマークして、階層内の任意の型に対してクエリを実行するためのフィルター述語を常に追加する必要があることを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="c564e-130">If you have such data, then you can mark the discriminator mapping in EF Core model as incomplete to indicate that we should always add filter predicate for querying any type in the hierarchy.</span></span> <span data-ttu-id="c564e-131">`IsComplete(false)` 識別子の構成でを呼び出すと、マッピングは不完全としてマークされます。</span><span class="sxs-lookup"><span data-stu-id="c564e-131">`IsComplete(false)` call on the discriminator configuration marks the mapping to be incomplete.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorMappingIncomplete.cs?name=DiscriminatorMappingIncomplete&highlight=5)]

### <a name="shared-columns"></a><span data-ttu-id="c564e-132">共有列</span><span class="sxs-lookup"><span data-stu-id="c564e-132">Shared columns</span></span>

<span data-ttu-id="c564e-133">既定では、階層内の2つの兄弟エンティティ型に同じ名前のプロパティがある場合、2つの異なる列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="c564e-133">By default, when two sibling entity types in the hierarchy have a property with the same name, they will be mapped to two separate columns.</span></span> <span data-ttu-id="c564e-134">ただし、型が同一の場合は、同じデータベース列にマップできます。</span><span class="sxs-lookup"><span data-stu-id="c564e-134">However, if their type is identical they can be mapped to the same database column:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]

## <a name="table-per-type-configuration"></a><span data-ttu-id="c564e-135">テーブルごとの構成</span><span class="sxs-lookup"><span data-stu-id="c564e-135">Table-per-type configuration</span></span>

> [!NOTE]
> <span data-ttu-id="c564e-136">TPT (テーブル単位) 機能は EF Core 5.0 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="c564e-136">The table-per-type (TPT) feature was introduced in EF Core 5.0.</span></span> <span data-ttu-id="c564e-137">EF6 では、具象型 (TPC) ごとにサポートされていますが、まだ EF Core サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="c564e-137">Table-per-concrete-type (TPC) is supported by EF6, but is not yet supported by EF Core.</span></span>

<span data-ttu-id="c564e-138">TPT mapping パターンでは、すべての型が個々のテーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="c564e-138">In the TPT mapping pattern, all the types are mapped to individual tables.</span></span> <span data-ttu-id="c564e-139">基本データ型または派生型のみに属するプロパティは、その型にマップされたテーブルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="c564e-139">Properties that belong solely to a base type or derived type are stored in a table that maps to that type.</span></span> <span data-ttu-id="c564e-140">派生型にマップされるテーブルには、派生テーブルとベーステーブルを結合する外部キーも格納されます。</span><span class="sxs-lookup"><span data-stu-id="c564e-140">Tables that map to derived types also store a foreign key that joins the derived table with the base table.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TPTConfiguration.cs?name=TPTConfiguration)]

<span data-ttu-id="c564e-141">EF は、上記のモデルに対して次のデータベーススキーマを作成します。</span><span class="sxs-lookup"><span data-stu-id="c564e-141">EF will create the following database schema for the model above.</span></span>

```sql
CREATE TABLE [Blogs] (
    [BlogId] int NOT NULL IDENTITY,
    [Url] nvarchar(max) NULL,
    CONSTRAINT [PK_Blogs] PRIMARY KEY ([BlogId])
);

CREATE TABLE [RssBlogs] (
    [BlogId] int NOT NULL,
    [RssUrl] nvarchar(max) NULL,
    CONSTRAINT [PK_RssBlogs] PRIMARY KEY ([BlogId]),
    CONSTRAINT [FK_RssBlogs_Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [Blogs] ([BlogId]) ON DELETE NO ACTION
);
```

> [!NOTE]
> <span data-ttu-id="c564e-142">Primary key 制約の名前を変更すると、階層にマップされているすべてのテーブルに新しい名前が適用されます。将来の EF バージョンでは、 [問題 19970](https://github.com/dotnet/efcore/issues/19970) が修正されると、特定のテーブルに対してのみ制約の名前を変更できるようになります。</span><span class="sxs-lookup"><span data-stu-id="c564e-142">If the primary key constraint is renamed the new name will be applied to all the tables mapped to the hierarchy, future EF versions will allow renaming the constraint only for a particular table when [issue 19970](https://github.com/dotnet/efcore/issues/19970) is fixed.</span></span>

<span data-ttu-id="c564e-143">一括構成を使用している場合は、を呼び出して、特定のテーブルの列名を取得でき <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="c564e-143">If you are employing bulk configuration you can retrieve the column name for a specific table by calling <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName%2A>.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TPTConfiguration.cs?name=Metadata&highlight=10)]
