---
title: 継承-EF Core
description: Entity Framework Core を使用してエンティティ型の継承を構成する方法
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 10/27/2016
uid: core/modeling/inheritance
ms.openlocfilehash: 0e94013a0b894b162f4bb3ca8e7acb1aca349011
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664053"
---
# <a name="inheritance"></a><span data-ttu-id="3c355-103">継承</span><span class="sxs-lookup"><span data-stu-id="3c355-103">Inheritance</span></span>

<span data-ttu-id="3c355-104">EF では、.NET 型階層をデータベースにマップできます。</span><span class="sxs-lookup"><span data-stu-id="3c355-104">EF can map a .NET type hierarchy to a database.</span></span> <span data-ttu-id="3c355-105">これにより、基本型と派生型を使用して、通常どおりに .NET エンティティをコードに記述し、EF によって適切なデータベーススキーマの作成、クエリの実行などをシームレスに行うことができます。型階層のマッピング方法の実際の詳細は、プロバイダーに依存します。このページでは、リレーショナルデータベースのコンテキストでの継承のサポートについて説明します。</span><span class="sxs-lookup"><span data-stu-id="3c355-105">This allows you to write your .NET entities in code as usual, using base and derived types, and have EF seamlessly create the appropriate database schema, issue queries, etc. The actual details of how a type hierarchy is mapped are provider-dependent; this page describes inheritance support in the context of a relational database.</span></span>

<span data-ttu-id="3c355-106">現時点では、EF Core では、階層単位 (TPH) のパターンのみがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="3c355-106">At the moment, EF Core only supports the table-per-hierarchy (TPH) pattern.</span></span> <span data-ttu-id="3c355-107">TPH は、1つのテーブルを使用して階層内のすべての型のデータを格納します。また、識別子列を使用して、各行が表す型を識別します。</span><span class="sxs-lookup"><span data-stu-id="3c355-107">TPH uses a single table to store the data for all types in the hierarchy, and a discriminator column is used to identify which type each row represents.</span></span>

> [!NOTE]
> <span data-ttu-id="3c355-108">EF6 によってサポートされている、型ごとのテーブル (TPT) と具象型 (TPC) は、EF Core ではまだサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="3c355-108">The table-per-type (TPT) and table-per-concrete-type (TPC), which are supported by EF6, are not yet supported by EF Core.</span></span> <span data-ttu-id="3c355-109">TPT は EF Core 5.0 に対して計画されている主な機能です。</span><span class="sxs-lookup"><span data-stu-id="3c355-109">TPT is a major feature planned for EF Core 5.0.</span></span>

## <a name="entity-type-hierarchy-mapping"></a><span data-ttu-id="3c355-110">エンティティ型階層のマッピング</span><span class="sxs-lookup"><span data-stu-id="3c355-110">Entity type hierarchy mapping</span></span>

<span data-ttu-id="3c355-111">慣例により、EF は基本型または派生型を自動的にスキャンしません。これは、階層内の CLR 型をマップする必要がある場合は、モデルでその型を明示的に指定する必要があることを意味します。</span><span class="sxs-lookup"><span data-stu-id="3c355-111">By convention, EF will not automatically scan for base or derived types; this means that if you want a CLR type in your hierarchy to be mapped, you must explicitly specify that type on your model.</span></span> <span data-ttu-id="3c355-112">たとえば、階層の基本データ型だけを指定しても、EF Core によってそのすべてのサブ型が暗黙的に含まれるとは限りません。</span><span class="sxs-lookup"><span data-stu-id="3c355-112">For example, specifying only the base type of a hierarchy will not cause EF Core to implicitly include all of its sub-types.</span></span>

<span data-ttu-id="3c355-113">次の例では、とそのサブクラスの DbSet を公開して `Blog` `RssBlog` います。</span><span class="sxs-lookup"><span data-stu-id="3c355-113">The following sample exposes a DbSet for `Blog` and its subclass `RssBlog`.</span></span> <span data-ttu-id="3c355-114">`Blog`に他のサブクラスがある場合は、モデルには含まれません。</span><span class="sxs-lookup"><span data-stu-id="3c355-114">If `Blog` has any other subclass, it will not be included in the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

<span data-ttu-id="3c355-115">このモデルは、次のデータベーススキーマにマップされます (それぞれの行に格納されている*ブログ*の種類を識別する、暗黙的に作成された*識別子*の列に注意してください)。</span><span class="sxs-lookup"><span data-stu-id="3c355-115">This model is mapped to the following database schema (note the implicitly-created *Discriminator* column, which identifies which type of *Blog* is stored in each row):</span></span>

![image](_static/inheritance-tph-data.png)

>[!NOTE]
> <span data-ttu-id="3c355-117">TPH マッピングを使用する場合、データベース列は必要に応じて自動的に null 許容になります。</span><span class="sxs-lookup"><span data-stu-id="3c355-117">Database columns are automatically made nullable as necessary when using TPH mapping.</span></span> <span data-ttu-id="3c355-118">たとえば、 *RssUrl*列は null 値を許容します。通常の*ブログ*インスタンスにはそのプロパティがないためです。</span><span class="sxs-lookup"><span data-stu-id="3c355-118">For example, the *RssUrl* column is nullable because regular *Blog* instances do not have that property.</span></span>

<span data-ttu-id="3c355-119">階層内の1つ以上のエンティティに対して DbSet を公開しない場合は、Fluent API を使用してモデルに含まれていることを確認することもできます。</span><span class="sxs-lookup"><span data-stu-id="3c355-119">If you don't want to expose a DbSet for one or more entities in the hierarchy, you can also use the Fluent API to ensure they are included in the model.</span></span>

> [!TIP]
> <span data-ttu-id="3c355-120">規則に依存しない場合は、を使用して基本型を明示的に指定でき `HasBaseType` ます。</span><span class="sxs-lookup"><span data-stu-id="3c355-120">If you don't rely on conventions, you can specify the base type explicitly using `HasBaseType`.</span></span> <span data-ttu-id="3c355-121">また、を使用し `.HasBaseType((Type)null)` て、階層からエンティティ型を削除することもできます。</span><span class="sxs-lookup"><span data-stu-id="3c355-121">You can also use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="discriminator-configuration"></a><span data-ttu-id="3c355-122">識別子の構成</span><span class="sxs-lookup"><span data-stu-id="3c355-122">Discriminator configuration</span></span>

<span data-ttu-id="3c355-123">識別子の列の名前と種類と、階層内の各型を識別するために使用される値を構成できます。</span><span class="sxs-lookup"><span data-stu-id="3c355-123">You can configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

<span data-ttu-id="3c355-124">上の例では、EF によって、階層の基本エンティティに対して暗黙的に[シャドウプロパティ](xref:core/modeling/shadow-properties)として識別子が追加されています。</span><span class="sxs-lookup"><span data-stu-id="3c355-124">In the examples above, EF added the discriminator implicitly as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="3c355-125">このプロパティは、次のように構成できます。</span><span class="sxs-lookup"><span data-stu-id="3c355-125">This property can be configured like any other:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

<span data-ttu-id="3c355-126">また、識別子は、エンティティ内の通常の .NET プロパティにもマップできます。</span><span class="sxs-lookup"><span data-stu-id="3c355-126">Finally, the discriminator can also be mapped to a regular .NET property in your entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

## <a name="shared-columns"></a><span data-ttu-id="3c355-127">共有列</span><span class="sxs-lookup"><span data-stu-id="3c355-127">Shared columns</span></span>

<span data-ttu-id="3c355-128">既定では、階層内の2つの兄弟エンティティ型に同じ名前のプロパティがある場合、2つの異なる列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="3c355-128">By default, when two sibling entity types in the hierarchy have a property with the same name, they will be mapped to two separate columns.</span></span> <span data-ttu-id="3c355-129">ただし、型が同一の場合は、同じデータベース列にマップできます。</span><span class="sxs-lookup"><span data-stu-id="3c355-129">However, if their type is identical they can be mapped to the same database column:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]
